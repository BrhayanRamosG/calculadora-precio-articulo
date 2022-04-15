<template>
  <h2 class="text-4xl text-white text-center">
    {{ msg }}
    {{
      new Intl.NumberFormat("es-MX", {
        style: "currency",
        currency: "MXN",
      }).format(precio)
    }}
    {{ divisa }}
  </h2>
</template>
<script>
export default {
  props: {
    msg: String,
    precio: Number,
    divisa: String,
  },
  setup(props) {
    return props;
  },
};
</script>
