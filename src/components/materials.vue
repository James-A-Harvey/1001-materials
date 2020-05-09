<template>
    <div class="h-full">
        <header class="bg-blue-700 border-b-2 border-blue-900">
            <div class="text-4xl container mx-auto px-2 py-2 text-white"> 1001 assignment 2 code </div>
        </header>
        <div class="bg-blue-100 h-full">
            <div class="text-2xl font-bold container mx-auto py-5"> Min area values calculator</div>
            <div class="container grid grid-cols-3 grid-rows-3 gap-2 mx-auto py-5">

                <form class="grid grid-rows-3 grid-cols-2 gap-2" v-on:submit.prevent="calculate">
                    <label for="name" class="px-2 text-center"> name </label>
                    <input type="text" id="name" required v-model="name" class="w-32 px-2">

                    <label for="yieldStress" class="text-center"> yield stress(MPa): </label>
                    <input type="number" id="yieldStress" required v-model="yieldStress" class="w-32 px-2">

                    <label for="youngMod" class="px-2 text-center"> Youngs Modulus (GPa) </label>
                    <input id="youngMod" type="number" required v-model="youngsMod" class="w-32 px-2">
                    <button type="submit" class="bg-blue-700 px-2 py-2 rounded"> submit </button>
                </form>
                <div class="col-start-2 col-span-2">
                    <table class="w-full table-auto">
                        <tr v-for="(row, idx) in currentMaterials" :key="idx">
                            <td v-for="(item, idx2) in row" :key="idx2" class="border-2 text-right" v-bind:class="{'bg-blue-200 text-center font-medium':idx===0}"> {{item}} </td>
                        </tr>
                    </table>
                </div>
                <h2 class="text-2xl font-bold py-5">
                    I calculator
                </h2>
            </div>
        </div>
    </div>
</template>

<script>

    const FORCE_ACTING = 80000;
    const LENGTH = 1400;


    class Material {
        constructor(name = '', yieldStress, youngsMod) {
            this._name = name;
            this._ystress = Number(yieldStress);
            this._youngsMod = Number(youngsMod)*1000; //GPa to Mpa
        }

        minArea () {
            return (FORCE_ACTING/this._ystress).toFixed(2)
        }

        bucklingSecoundMomentOfArea () {
            let pisqrd = (Math.pow(Math.PI,2));
            let I = FORCE_ACTING*Math.pow(LENGTH,2)/(pisqrd*Number(this._youngsMod));
            return `${(I/Math.pow(10,6)).toFixed(2)}*10^6 mm^4`
        }

        toLst() {
            let minArea = this.minArea();
            return [`${this._name}`, `${this._ystress}MPa`, `${minArea}mm^2`, `${this.bucklingSecoundMomentOfArea()}`]
        }
    }
    export default {
        name: "materials",
        data() {
            return {
                name: '',
                yieldStress: undefined,
                youngsMod: undefined,
                currentMaterials: [['name', 'yield stress', 'min area', 'min I value']],
            }
        },
        methods: {
            calculate() {
                let material = new Material(this.name, this.yieldStress, this.youngsMod);
                this.currentMaterials.push(material.toLst())

            }
        }
    }
</script>

<style>

</style>
