<template>
  <div v-if="products.length > 0">
    <h3 class="font-bold text-2xl text-center text-white mb-4">
      ---Lista de productos---
    </h3>
    <ul :class="['list-decimal', products.length > 11 && 'md:columns-3']">
      <li
        class="first-letter:uppercase text-left text-2xl text-white"
        v-for="(product, index) in products"
        :key="index"
      >
        {{ product.product }} - ${{ product.priceProduct }} kg <br />
        x({{ product.quantity }}) ({{
          product.metricProduct + " " + product.metric
        }})
        {{
          new Intl.NumberFormat("es-MX", {
            style: "currency",
            currency: "MXN",
          }).format(product.price)
        }}
        MXN
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  props: {
    products: Array,
  },
  setup(props) {
    return { props };
  },
};
</script>
