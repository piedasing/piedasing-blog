---
title: nuxt-demo
date: 2019-04-17 17:15:51
tags: Nuxt、Vue、SSR
---

### 相關工具、套件、技術
*Nuxt、Vue、SSR*

### 目的
> 1. 使用 Nuxt ，製作具有 SSR 的簡單電商網頁

### 步驟

1. 新建 nuxt-app 專案
   ``` bash
    npx create-nuxt-app <專案名稱>
   ```
---
2. 安裝套件
   ``` bash
    // 安裝 sass 跟 pug
    npm install node-sass sass-loader pug pug-plain-loader
    // 安裝 json-server
    npm install json-server
    // 安裝 style-resources (在每個.vue檔載入sass/scss檔)
    npm install @nuxtjs/style-resources
   ```
3. 在 assets 資料夾新增樣式
  > ![asset資料夾結構](nuxt-demo/asset.jpg)

  > _mixins.scss
  ``` scss
    @mixin size($w, $h: $w) {
      width: $w;
      height: $h;
    }

    @mixin flex($horizontal, $vertial: $horizontal) {
      display: flex;
      justify-content: $horizontal;
      align-items: $vertial;
    }

    @mixin bg($w, $h, $bgSize: contain) {
      @include size($w, $h);
      background-size: $bgSize;
      background-position: center center;
      background-repeat: no-repeat;
    }

    $color-primary: #2C4832;
    $color-secondary: #2C4832;
  ```
  > transition.css
  ``` css
    .page-enter-active, .page-leave-active {
      transition: all 0.5s ease-in-out;
    }
    .page-enter, .page-leave-to {
      transform: rotate(180deg);
      opacity: 0;
    }
    .page-enter-to, .page-leave {
      opacity: 1;
    }

    .icon-enter-active, .icon-leave-active {
      transition: all 0.5s ease-in-out;
    }
    .icon-enter, .icon-leave-to {
      transform: scale(1.5);
      opacity: 0;
    }
    .icon-enter-to, .icon-leave {
      opacity: 1;
    }
  ```
4. 到 nuxt.config.js 載入樣式
  ``` javascript
    head: {
      title: pkg.name,
      meta: [
        { charset: 'utf-8' },
        { name: 'viewport', content: 'width=device-width, initial-scale=1' },
        { hid: 'description', name: 'description', content: pkg.description }
        // 這裡可以新增要放的 meta 資訊
      ],
      link: [
        { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' },
        // 載入 fontawesome 樣式
        { rel: 'stylesheet', href: 'https://use.fontawesome.com/releases/v5.8.1/css/all.css' },
        // 載入 bootstrap 樣式
        { rel: 'stylesheet', href: 'https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css' }
      ]
    },
    css: [
      '~/assets/transition.css' // 載入全域 css 檔
    ],
    modules: [
      // Doc: https://axios.nuxtjs.org/usage
      '@nuxtjs/axios',
      '@nuxtjs/style-resources' // 載入style-resources 模組
    ],
    /*
    ** import sass/scss
    */
    styleResources: {
      scss: [
        '~/assets/scss/_mixins.scss' // style-resources 會在全部的.vue檔載入這支 scss
      ]
    },

  ```
5. 在 pages 資料夾下新增 product.vue
  ``` javascript
    <template lang='pug'>
      section.product
        nav
          ol.breadcrumb
            li.breadcrumb-item
              nuxt-link(to="/") 首頁
            li.breadcrumb-item.active 產品
        .container
          .row.w-100
            template(v-for="product in products")
              .col-12.col-md-6.col-lg-4
                Card(:product="product")
    </template>

    <script>
    import Card from '~/components/Card'
    import { productAPI } from '~/httpService'

    export default {
      name: 'Product',
      components: {
        Card
      },
      async asyncData({isDev, route, store, env, params, query, req, res, redirect, error}) {
        const { getProductList } = productAPI
        let { data } = await getProductList()
        return {
          products: data
        }
      },
      data() {
        return {
          products: []
        }
      }
    }
    </script>

    <style lang='scss'>
    .product {
      padding: 1rem 5%;
      nav {
        max-width: 1140px;
        padding-left: 30px;
        padding-right: 30px;
        margin: 0 auto;
      }
      .row{
        margin: 0 auto;
      }
    }
    </style>
  ```
6. 在 components 資料夾下新增 Card.vue
  ``` javascript
    <template lang='pug'>
      .card.mb-3
        .img.card-img-top(:style="showImage(product.image)")
          label.feature 本日精選
          transition-group(name="icon")
            i.heart.far.fa-heart(v-if="!isHover" @click="isHover = true" :key="0")
            i.heart.fas.fa-heart(v-else @click="isHover = false" :key="1")
        .card-body
          .card-info
            h6
              span.card-title {{ product.name }}
              span.card-text {{ product.price | currency }}
          a.addToCart.btn.w-100 加入購物車
    </template>

    <script>
    export default {
      name: 'Card',
      props: {
        product: {
          type: Object,
          required: true
        }
      },
      data() {
        return {
          isHover: false
        }
      },
      computed: {
        showImage() {
          return function(url) {
            return { 'background-image': `url('${url}')` }
          }
        }
      },
      filters: {
        currency(num) {
          const n = Number(num)
          return `NT.${n.toFixed(0).replace(/./g, (c, i, a) => {
            const currency = (i && c !== '.' && ((a.length - i) % 3 === 0) ? `, ${c}`.replace(/\s/g, '') : c)
            return currency
          })}元`
        }
      },
      methods: {
      }
    }
    </script>

    <style lang='scss'>
    .card {
      .img {
        @include bg(100%, 200px, cover);
        position: relative;
        .feature {
          background-color: $color-primary;
          color: #FFF;
          writing-mode: vertical-lr;
          padding: 1rem 0.5rem;
          letter-spacing: 5px;
          position: absolute;
          top: -0.5rem;
          left: 1rem;
        }
        .heart {
          display: block;
          background-color: #FFF;
          padding: 0.5rem;
          border-bottom-left-radius: 15px;
          position: absolute;
          top: -1px;
          right: -1px;
        }
      }
      .card-info {
        display: flex;
        margin-bottom: 1rem;
        h6 {
          flex: auto;
          @include flex(center);
          span {
            flex: 1;
            text-align: center;
            margin: 0;
          }
          .card-text {
            border-left: 1px solid $color-primary;
          }
        }
      }
      .addToCart {
        color: $color-secondary;
        background-color: #EAF0ED;
      }
    }
    </style>

  ```
7. 在根目錄新增 httpService 資料夾(存放 api 要用到的資料)
  > ![httpService資料夾](nuxt-demo/api.jpg)
8. 1
9.  
