<template>
    <div class="modalContent">
        <h1>{{ heading }}</h1>
        <form>
            <label>Tytuł filmu</label>
            <input v-model="title"/>
            <label>Rok produkcji (od 1900 do 2100)</label>
            <input v-model="productionYear"/>
            <button :disabled="$v.$invalid" @click="buttonAction">{{ buttonText }}</button>
        </form>
    </div>
</template>

<script>
import { validationMixin } from "vuelidate"
import { required, maxLength, minValue, maxValue } from "vuelidate/lib/validators"
import axios from 'axios'

export default {
    name: "ModalContent",
    mixins: [validationMixin],
    props: ["title", "productionYear", "buttonAction"],
    data() {
        return {

        }
    },
    validations: {
        title: {
            required,
            maxLength: maxLength(200)
        },
        productionYear: {
            minValue: minValue(1900),
            maxValue: maxValue(2100)
        }
    },
    methods: {
        addMovie: async function () {
            await axios.post("https://localhost:44353/api/movie", {
                title: this.newTitle,
                productionYear: this.newProductionYear
            });
        },

        editMovie: async function () {
            await axios.post("https://localhost:44353/api/movie", {
                title: this.newTitle,
                productionYear: this.newProductionYear
            });
        }


    }   
}
</script>