<template>
  <div class="grid grid-cols-1 gap-4 place-items-center">
    <div class="mt-10">
      <h1 class="text-6xl font-bold text-white text-center">
        &iquest;Cu&aacute;nto es?
      </h1>
    </div>
    <div class="mt-10">
      <h2 class="text-2xl text-white text-center">
        Calcula el costo de tu producto en gramos o kilogramos.
      </h2>
    </div>
    <div class="mt-0">
      <div class="w-[95vw] xl:w-[85vw] md:grid md:grid-cols-1 md:gap-6">
        <div class="mt-5">
          <form @submit="addProduct">
            <div class="shadow overflow-hidden sm:rounded-md">
              <div class="px-4 py-5 bg-slate-700 sm:p-6">
                <div class="grid grid-cols-6 gap-6">
                  <div class="col-span-6 sm:col-span-3">
                    <label
                      for="nombre-producto"
                      class="block text-3xl font-medium text-gray-100 mb-4"
                      >Nombre producto</label
                    >
                    <InputCalculadora
                      type="text"
                      v-model="form.product"
                      placeholder="Ingresar producto"
                    />
                  </div>

                  <div class="col-span-6 sm:col-span-3">
                    <label
                      for="medida"
                      class="block text-3xl font-medium text-gray-100 mb-4"
                      >Unidad de medida</label
                    >
                    <select
                      id="medida"
                      name="medida"
                      class="mt-1 block w-full py-2 px-3 text-3xl text-white border border-gray-300 bg-slate-500 rounded-md shadow-sm focus:outline-none focus:ring-cyan-400 focus:border-cyan-400"
                      v-model="form.metric"
                    >
                      <option value="" selected>Selecciona una opcion</option>
                      <option value="1">Gramos</option>
                      <option value="2">Kilogramos</option>
                    </select>
                  </div>

                  <div class="col-span-6 sm:col-span-3">
                    <label
                      for="precio"
                      class="block text-3xl font-medium text-gray-100 mb-4"
                      >Precio por kg</label
                    >
                    <InputCalculadora
                      type="number"
                      step="0.1"
                      min="0"
                      v-model="form.price"
                      placeholder="Ingresar precio"
                    />
                  </div>

                  <div class="col-span-6 sm:col-span-3">
                    <label
                      for="metrica"
                      class="first-letter:uppercase block text-3xl font-medium text-gray-100 mb-4"
                      >{{
                        form.nameMetric[form.metric ? form.metric : 0]
                      }}</label
                    >
                    <InputCalculadora
                      type="number"
                      step="0.1"
                      min="0"
                      v-model="form.metricProduct"
                      :placeholder="`${
                        !form.metric
                          ? 'Selecciona unidad de medida'
                          : 'Ingresa ' +
                            form.nameMetric[form.metric ? form.metric : 0]
                      }`"
                      :disabled="!form.metric"
                    />
                  </div>

                  <div class="col-span-6 sm:col-span-3">
                    <label
                      for="cantidad"
                      class="first-letter:uppercase block text-3xl font-medium text-gray-100 mb-4"
                      >Cantidad</label
                    >
                    <InputCalculadora
                      type="number"
                      min="1"
                      v-model="form.quantity"
                      placeholder="Ingrese cantidad"
                      :disabled="!form.metric"
                    />
                  </div>
                </div>
              </div>
              <div
                class="flex gap-4 justify-center items-center px-4 py-3 bg-slate-400 text-right sm:px-6"
              >
                <div class="">
                  <ButtonPrincipal
                    :class="[
                      !calculatePrice || !form.product
                        ? 'bg-gray-300 text-gray-500'
                        : 'bg-green-600 hover:bg-green-700 text-white',
                    ]"
                    msg="Agregar"
                    type="submit"
                    @click="addProduct"
                    :disabled="!form.product || (!calculatePrice && true)"
                  />
                </div>
                <div>
                  <ButtonPrincipal
                    :class="[
                      !form.price && !form.metricProduct
                        ? 'bg-gray-300 text-gray-500'
                        : 'bg-gray-600 hover:bg-gray-700 text-white',
                    ]"
                    msg="Limpiar"
                    type="button"
                    @click="reset"
                    :disabled="!form.price && !form.metricProduct && true"
                  />
                </div>
                <div>
                  <ButtonPrincipal
                    :class="[
                      !calculateTotal
                        ? 'bg-gray-300 text-gray-500'
                        : 'bg-rose-600 hover:bg-rose-700 text-white',
                    ]"
                    msg="Eliminar lista"
                    type="button"
                    @click="deleteArray"
                    :disabled="!calculateTotal && true"
                  />
                </div>
              </div>
            </div>
          </form>
        </div>
      </div>
    </div>

    <div class="w-[95vw] xl:w-[85vw] bg-slate-600 rounded-md mb-8 lg:mb-10">
      <div class="px-4 py-4">
        <MostrarPrecio
          v-if="calculatePrice"
          class="mb-4 lowercase first-letter:uppercase"
          :msg="`costo ${form.product}`"
          :precio="calculatePrice"
        />
        <div class="px-4 py-4 mx-2 my-2">
          <MostrarLista :products="productArray" />
        </div>
        <MostrarPrecio
          class="font-bold text-right"
          msg="Total:"
          :precio="calculateTotal"
          divisa="MXN"
        />
      </div>
    </div>
  </div>
</template>
<script>
import { reactive, ref, computed } from "vue";
import ButtonPrincipal from "./ButtonPrincipal.vue";
import InputCalculadora from "./InputCalculadora.vue";
import MostrarPrecio from "./MostrarPrecio.vue";
import MostrarLista from "./MostrarLista.vue";

export default {
  components: {
    ButtonPrincipal,
    InputCalculadora,
    MostrarPrecio,
    MostrarLista,
  },
  setup() {
    const initialState = {
      product: "",
      price: "",
      metricProduct: "",
      quantity: "1",
      metric: "",
      nameMetric: ["medida", "gramos", "kilogramos"],
    };
    const productArray = ref([]);
    const form = reactive({ ...initialState });

    const resetForm = () => {
      const { quantity, product, price, metricProduct } = initialState;
      Object.assign(form, { quantity, product, price, metricProduct });
    };

    const reset = () => {
      resetForm();
    };

    const calculateMetric = computed(() => {
      let metric;
      const productQuantity = form.metricProduct * form.quantity;
      if ((form.metric === "1" && productQuantity > 999) || form.metric === "2")
        metric = "kg";
      else metric = "g";
      return metric;
    });

    const converterToKg = computed(() => {
      let result;
      if (form.metric === "1" && form.metricProduct * form.quantity >= 1000)
        result = (form.metricProduct / 1000) * form.quantity;
      else if (form.metric !== "") result = form.metricProduct * form.quantity;
      return result;
    });

    const addProduct = (e) => {
      e.preventDefault();
      productArray.value.push({
        product: form.product,
        metricProduct: converterToKg.value,
        price: calculatePrice.value,
        quantity: form.quantity,
        priceProduct: form.price,
        metric: calculateMetric.value,
      });
      resetForm();
    };

    const deleteArray = () => {
      productArray.value = [];
    };

    const calculateTotal = computed(() => {
      let result = 0;
      productArray.value.forEach((element) => {
        result += element.price;
      });
      return parseFloat(result.toFixed(2));
    });

    const calculatePrice = computed(() => {
      let result = 0;
      if (form.metric === "1")
        result = form.price * (form.metricProduct / 1000) * form.quantity;
      else if (form.metric === "2")
        result = form.price * form.metricProduct * form.quantity;
      return parseFloat(result.toFixed(2));
    });

    return {
      form,
      addProduct,
      reset,
      deleteArray,
      calculatePrice,
      calculateTotal,
      productArray,
    };
  },
};
</script>
