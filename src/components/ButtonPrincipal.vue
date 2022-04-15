<template>
  <button class="py-2 px-2 text-2xl rounded-md" :type="type">
    {{ msg }}
  </button>
</template>
<script>
export default {
  props: {
    msg: String,
    type: String,
  },
  setup(props) {
    return { props };
  },
};
</script>
