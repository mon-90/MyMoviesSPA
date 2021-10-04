<template>
    <div class="modal">
        <button class="closeBtn" @click="closeModal">X</button>

        <div class="modalContent" v-if="actionName === 'addMovie'">
            <h1 class="heading">Nowy film</h1>
            <form>
                <div class="formGroup">
                    <label >Tytuł filmu</label>
                    <input v-model="newTitle"/>
                </div>
                
                <div class="formGroup">
                    <label>Rok produkcji (od 1900 do 2100)</label>
                    <input v-model="newProductionYear"/>
                </div>
                <button class="button" :disabled="($v.newTitle.$invalid || $v.newProductionYear.$invalid)" @click="addMovie">Dodaj</button>
            </form>
        </div>

        <div class="modalContent" v-if="actionName === 'editMovie'">
            <h1 class="heading editHeading">Edycja filmu:</h1>
            <h3 class="smHeading">{{ movie.title }} - {{ movie.productionYear }}</h3>
            <form>
                <div class="formGroup">
                    <label>Tytuł filmu</label>
                    <input v-model="currentTitle" />
                </div>
                <div class="formGroup">
                    <label>Rok produkcji (od 1900 do 2100)</label>
                    <input v-model="currentProductionYear" />
                </div>
                <button class="button" :disabled="($v.currentTitle.$invalid || $v.currentProductionYear.$invalid)" @click="editMovie">Zapisz</button>
            </form>
        </div>

        <div class="modalContent" v-if="actionName === 'showMovie'">
            <h1 class="heading">Podgląd filmu</h1>
            <div class="group">
                <h3 class="showHeading">Tytuł filmu:</h3>
                <h3 class="showHeading">{{ movie.title }}</h3>
            </div>
            <div class="group">
                <h3 class="showHeading">Rok produkcji:</h3>
                <h3 class="showHeading">{{ movie.productionYear }}</h3>
            </div> 
        </div>

    </div>
</template>

<script>
import { validationMixin } from "vuelidate"
import { required, maxLength, minValue, maxValue } from "vuelidate/lib/validators"
import axios from 'axios'

export default {
    name: "MovieModal",
    mixins: [validationMixin],
    props: ["actionName", "movie", "movieId"],
    
    data() {
        return {
            modalIsOpen: true,
            newTitle: "",
            newProductionYear: null,
            currentTitle: "",
            currentProductionYear: null,
            isCorrectString: true
        }
    },

    validations: {
        newTitle: {
            required,
            maxLength: maxLength(200)
        },
        newProductionYear: {
            minValue: minValue(1900),
            maxValue: maxValue(2100)
        },
        currentTitle: {
            required,
            maxLength: maxLength(200)
        },
        currentProductionYear: {
            minValue: minValue(1900),
            maxValue: maxValue(2100)
        }
    },

    methods: {
        closeModal() {
            this.modalIsOpen = false;
            this.$emit("open", this.modalIsOpen);
        },
        addMovie: async function () {
            this.stringVerification(this.newTitle);
            if(this.isCorrectString) {
                await axios.post("https://localhost:44353/api/movie", {
                title: this.newTitle,
                productionYear: this.newProductionYear
                });
            }
            this.isCorrectString = true;
            this.newTitle = "";
            this.newProductionYear = null;
        },
        editMovie: async function () {
            this.stringVerification(this.currentTitle);
            if(this.isCorrectString){
                await axios.put(`https://localhost:44353/api/movie/${this.movie.id}`, {
                title: this.currentTitle,
                productionYear: this.currentProductionYear
                });
            }
            this.isCorrectString = true;
            this.currentTitle = "";
            this.currentProductionYear = null;
        },
        stringVerification(str) {
            [...str].forEach(element => {
                if(element === "'"){
                    alert("Błędny tytuł");
                    this.isCorrectString = false;
                    this.closeModal();
                } 
            });
        }
    }   
}
</script>

<style scoped>

*{
    margin: 0;
    padding: 0;
}

.modal {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -70%);
    height: 30%;
    width: 30%;
    min-height: 20rem;
    min-width: 20rem;
    background-color: rgb(255, 237, 203);
    border-radius: 3px;
    box-shadow: 8px 8px 24px 0px rgba(66, 68, 90, 1);
}

.closeBtn {
    position: absolute;
    top: 1rem;
    right: 1rem;
    width: 2rem;
    height: 2rem;
    padding: 5px;
    background-color: #d96675;
    border: none;
    border-radius: 5px;
    font-weight: 900;
    cursor: pointer;
}

.heading {
    padding-top: 1.5rem;
    padding-bottom: 1.5rem;
    text-align: center;
    text-transform: uppercase;
    font-weight: 500;
}

.editHeading {
    padding-bottom: 0.1rem;
}

.smHeading {
    padding-bottom: 1rem;
    text-align: center;
}

.formGroup {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding-bottom: 1rem;
    font-size: 1.3rem;
}

label {
    padding-bottom: 0.5rem;
}

input {
    display: inline-block;
    width: 50%;
}

.button {
    position: absolute;
    bottom: 2rem;
    left: 50%;
    transform: translate(-50%);
    padding: 0.5rem;
    background-color: #4fa833;
    border: none;
    border-radius: 10px;
    color: #fff;
    font-size: 1.1rem;
    cursor: pointer;
}

.group {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding-bottom: 2rem;
}

.showHeading {
    font-size: 1.2rem;
    font-weight: 500;
}

</style>