<template>
  <input
    class="block w-full bg-slate-500 text-white text-3xl rounded-md focus:border-cyan-400 focus:ring-cyan-400 focus:bg-slate-400 focus:placeholder:text-slate-600 placeholder:text-slate-300"
    :type="type"
    :value="modelValue"
    @input="updateValue"
    :placeholder="placeholder"
  />
</template>

<script>
export default {
  props: {
    type: String,
    placeholder: String,
    modelValue: String,
    format: Boolean,
  },
  setup(props, context) {
    const updateValue = (event) => {
      context.emit("update:modelValue", event.target.value);
    };
    return { props, updateValue };
  },
};
</script>
