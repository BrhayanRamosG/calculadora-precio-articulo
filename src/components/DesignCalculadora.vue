<template>
  <div class="grid grid-cols-1 gap-4 place-items-center">
    <div class="mt-10">
      <h1 class="text-6xl text-white text-center">&iquest;Cu&aacute;nto es?</h1>
    </div>
    <div class="mt-10">
      <h2 class="text-2xl text-white text-center">
        Ingresa el precio y peso de tu art&iacute;culo, en gramos o kilogramos.
      </h2>
    </div>
    <div class="mt-10">
      <div class="w-[95vw] xl:w-[85vw] md:grid md:grid-cols-1 md:gap-6">
        <div class="mt-5">
          <form @submit="addPrice">
            <div class="shadow overflow-hidden sm:rounded-md">
              <div class="px-4 py-5 bg-slate-700 sm:p-6">
                <div class="grid grid-cols-6 gap-6">
                  <div class="col-span-6 sm:col-span-3">
                    <label
                      for="first-name"
                      class="block text-4xl font-medium text-gray-100 mb-4"
                      >Precio</label
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
                      for="last-name"
                      class="block text-4xl font-medium text-gray-100 mb-4"
                      >{{
                        form.nameMetric[form.metric ? form.metric : 0]
                      }}</label
                    >
                    <InputCalculadora
                      type="number"
                      step="0.1"
                      min="0"
                      v-model="form.quantity"
                      placeholder="Ingresar cantidad"
                    />
                  </div>

                  <div class="col-span-6">
                    <label
                      for="medida"
                      class="block text-4xl font-medium text-gray-100 mb-4"
                      >Unidad de medida</label
                    >
                    <select
                      id="medida"
                      name="medida"
                      class="mt-1 block w-full py-2 px-3 text-4xl text-white border border-gray-300 bg-slate-500 rounded-md shadow-sm focus:outline-none focus:ring-cyan-400 focus:border-cyan-400"
                      v-model="form.metric"
                    >
                      <option value="" selected>Selecciona una opcion</option>
                      <option value="1">Gramos</option>
                      <option value="2">Kilogramos</option>
                    </select>
                  </div>
                </div>
              </div>
              <div
                class="flex gap-4 justify-center items-center px-4 py-3 bg-slate-400 text-right sm:px-6"
              >
                <div class="">
                  <ButtonPrincipal
                    :class="[
                      !calculatePrice
                        ? 'bg-gray-300 text-gray-500'
                        : 'bg-green-600 hover:bg-green-700 text-white',
                    ]"
                    msg="Agregar"
                    type="submit"
                    @click="addPrice"
                    :disabled="!calculatePrice && true"
                  />
                </div>
                <div>
                  <ButtonPrincipal
                    :class="[
                      !form.price && !form.quantity
                        ? 'bg-gray-300 text-gray-500'
                        : 'bg-gray-600 hover:bg-gray-700 text-white',
                    ]"
                    msg="Limpiar"
                    type="button"
                    @click="reset"
                    :disabled="!form.price && !form.quantity && true"
                  />
                </div>
                <div>
                  <ButtonPrincipal
                    :class="[
                      !calculateTotal
                        ? 'bg-gray-300 text-gray-500'
                        : 'bg-rose-600 hover:bg-rose-700 text-white',
                    ]"
                    msg="Borrar total"
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

    <div class="bg-slate-700 rounded-md mb-8 lg:mb-0">
      <div class="px-4 py-4">
        <MostrarPrecio
          msg="Precio artículo:"
          :precio="calculatePrice"
          divisa="MXN"
        />
        <MostrarPrecio
          class="font-bold"
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

export default {
  components: { ButtonPrincipal, InputCalculadora, MostrarPrecio },
  setup() {
    const initialState = {
      price: "",
      quantity: "",
      metric: "",
      nameMetric: ["Métrica", "Gramos", "Kilogramos"],
    };
    const priceArray = ref([]);
    const form = reactive({ ...initialState });

    const resetForm = () => {
      const { price, quantity } = initialState;
      Object.assign(form, { price, quantity });
    };

    const reset = () => {
      resetForm();
    };

    const addPrice = (e) => {
      e.preventDefault();
      priceArray.value.push(calculatePrice.value);
      resetForm();
    };

    const deleteArray = () => {
      priceArray.value = [];
    };

    const calculateTotal = computed(() => {
      let result = 0;
      priceArray.value.forEach((element) => {
        result += element;
      });
      return parseFloat(result.toFixed(2));
    });

    const calculatePrice = computed(() => {
      let result = 0;
      if (form.metric === "1") result = form.price * (form.quantity / 1000);
      else if (form.metric === "2") result = form.price * form.quantity;
      return parseFloat(result.toFixed(2));
    });

    return {
      form,
      addPrice,
      reset,
      deleteArray,
      calculatePrice,
      calculateTotal,
    };
  },
};
</script>
