<template>
    <div id="app">
        <header>
            <h1 v-if="is_valid">{{ percentage }}</h1>
            <h1 v-else>Bodyfat calculator</h1>

            <a @click.prevent="reset" v-show="any_entered" href="/">
                <img src="@/assets/reset.svg">
            </a>
        </header>

        <form>
            <div class="sex">
                <div>
                    <input type="radio" id="female" value="female" v-model="sex">
                    <label for="female">Female</label>
                </div>

                <div>
                    <input type="radio" id="male" value="male" v-model="sex">
                    <label for="male">Male</label>
                </div>
            </div>

            <BodyMetric
                name="naval"
                :measure="naval"
                @input="val => naval = val"
            />

            <BodyMetric
                name="neck"
                :measure="neck"
                @input="val => neck = val"
            />

            <BodyMetric
                name="height"
                :measure="height"
                @input="val => height = val"
            />

            <BodyMetric
                name="hip"
                :disabled="sex === 'male'"
                :measure="hip"
                @input="val => hip = val"
            />
        </form>
    </div>
</template>

<script>
import BodyMetric from './components/BodyMetric.vue';
export default {
    name: 'App',

    components: {
        BodyMetric,
    },

    data: () => ({
        sex: undefined,
        naval: '',
        neck: '',
        height: '',
        hip: '',
    }),

    computed: {
        // returns true if any form element has input
        any_entered() {
            return [
                this.sex,
                this.naval,
                this.neck,
                this.height,
                this.hip,
            ].some(_ => _);
        },

        // returns true if the form is entirely valid for a male/female
        is_valid() {
            if (this.naval && this.neck && this.height) {
                if (this.sex === 'female' && this.hip) return true;
                if (this.sex === 'male') return true;
            }

            return false;
        },

        // returns the bodyfat percentage
        percentage() {
            let result;
            let naval = parseFloat(this.naval);
            let neck = parseFloat(this.neck);
            let height = parseFloat(this.height);

            if (this.sex === 'female') {
                let hip = parseFloat(this.hip);

                result = (163.205 * Math.log10(naval + hip - neck) - 97.684 * Math.log10(height) - 104.912);
            } else {
                result = 86.010 * Math.log10(naval - neck) - 70.041 * Math.log10(height) + 30.30;
            }

            // if the result is "Infinite" or "NaN", do not show
            if (!isFinite(result)) {
                return 'Invalid';
            }

            return result.toFixed(2) + '%';
        }
    },

    methods: {
        // resets all form fields
        reset() {
            this.sex = undefined;
            this.naval = '';
            this.neck = '';
            this.height = '';
            this.hip = '';
        }
    },
}
</script>

<style>
    @font-face {
        font-family: 'Patrick Hand SC';
        font-weight: 400;
        font-style: normal;

        src: url('~@/assets/PatrickHand-Regular.ttf');
    }

    html, body {
        overflow-y: hidden;

        height: 100vh;
        margin: 0;
    }

    body {
        display: flex;
        align-items: center;
        justify-content: center;

        user-select: none;

        background: #f9fafb;
    }

    #app {
        font-family: 'Patrick Hand SC';

        width: 960px;
    }

    header {
        position: relative;
    }

    header > h1 {
        font-size: 3em;

        margin: 0;
        margin-bottom: 1em;
        padding: .5em 0;

        text-align: center;

        color: #fff;
        border: 2px solid #41403e;
        border-top-left-radius: 255px 15px;
        border-top-right-radius: 15px 225px;
        border-bottom-right-radius: 225px 15px;
        border-bottom-left-radius: 15px 255px;
        background: #4ecdc4;
        box-shadow: 15px 28px 25px -18px rgba(0, 0, 0, .2);
    }

    header > a {
        position: absolute;
        top: 40px;
        right: 40px;

        transition: .2s opacity;

        opacity: .5;
        outline: none;
    }

    header > a:hover {
        opacity: 1;
    }

    form {
        display: grid;

        width: 80%;
        margin: 0 auto;

        border: 2px solid #41403e;
        box-shadow: 15px 28px 25px -18px rgba(0,0,0,.2);

        grid-template-columns: repeat(2, 1fr);
        grid-template-rows: repeat(3, auto);
    }

    label {
        font-size: 2em;
    }

    form .sex, form .metric {
        padding: 1em;
    }

    form .sex {
        display: flex;
        justify-content: space-evenly;

        grid-column: span 2;
    }

    form .sex label, form .sex input {
        cursor: pointer;
        outline: none;
    }
</style>
