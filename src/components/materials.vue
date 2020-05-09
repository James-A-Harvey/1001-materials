<template>
    <div class="h-full">
        <header class="bg-blue-700 border-b-2 border-blue-900">
            <div class="text-4xl container mx-auto px-2 py-2 text-white"> 1001 assignment 2 code</div>
        </header>
        <div class="bg-blue-100 h-full">
            <div class="text-2xl font-bold container mx-auto py-5"> Min area values calculator</div>
            <div class="container grid grid-cols-3 gap-2 mx-auto py-5">

                <form class="grid grid-rows-3 grid-cols-2 gap-2" v-on:submit.prevent="calculate">
                    <label for="name" class="px-2 text-center"> name </label>
                    <input type="text" id="name" required v-model="name" class="w-32 px-2">

                    <label for="yieldStress" class="text-center"> yield stress(MPa): </label>
                    <input type="number" step="0.01" id="yieldStress" required v-model="yieldStress" class="w-32 px-2">

                    <label for="youngMod" class="px-2 text-center"> Youngs Modulus (GPa) </label>
                    <input id="youngMod" type="number" step="0.01" required v-model="youngsMod" class="w-32 px-2">
                    <button type="submit" class="bg-blue-700 px-2 py-2 rounded"> submit</button>
                </form>
                <div class="col-start-2 col-span-2">
                    <table class="w-full table-auto">
                        <tr v-for="(row, idx) in currentMaterials" :key="idx">
                            <td v-for="(item, idx2) in row" :key="idx2" class="border-2 text-right"
                                v-bind:class="{'bg-blue-200 text-center font-medium':idx===0}"> {{item}}
                            </td>
                        </tr>
                    </table>
                </div>
                <div class="col-start-1 col-span-3">
                    <h2 class="text-2xl font-bold pt-5 pb-2">
                        I calculator
                    </h2>
                    <span class="text-lg font-medium py-2">
                                Add shape
                            </span>
                    <form class="flex flex-row" v-on:submit.prevent="addToArr">
                        <div class="flex flex-col">

                            <label for="square" class="py-1">
                                <input id="square" type="radio" name="shapeType" value="square" v-model="radioType"> square
                            </label>
                            <label for="circle" class="py-1">
                                <input id="circle" type="radio" name="shapeType" value="circle" v-model="radioType"> circle
                            </label>
                        </div>
                        <div class=" px-5 flex flex-col w-auto" >
                            <div v-if="radioType==='circle'">
                                <label for="radius" class="py-1 flex flex-row">
                                    <span class="w-32"> radius(mm): </span>
                                    <input id="radius" v-model="radius" type="number" step="0.01" class="w-20 px-2">
                                </label>
                            </div>
                            <div v-else>
                            <label for="bValue" class="py-1 flex flex-row">
                                <span class="w-32"> b value(mm): </span>
                                <input id="bValue" v-model="bValue" type="number" step="0.01" class="w-20 px-2">
                            </label>
                            <label for="dValue" class="py-1  flex flex-row">
                                <span class="w-32"> d value(mm): </span>
                                <input id="dValue" v-model="dValue" type="number" step="0.01" class="w-20 px-2">
                            </label>
                            </div>
                            <label for="shiftValue" class="py-1 flex flex-row">
                                <span class="w-32"> bottom dist(mm): </span>

                                <input id="shiftValue" v-model="bottomDist" type="number" step="0.01" class="w-20 px-2">
                            </label>
                        </div>
                        <div>
                            <button type="submit" class="px-3 py-1 rounded bg-blue-700 text-white"> add shape </button>
                        </div>
                    </form>
                    <h2 class="text-xl font-medium"> I value </h2>
                    <div class="flex-row">
                        <div class="flex-col flex">
                            <span> yc(mm) = {{yc}}</span>
                            <span> I(mm) = {{iValue}}</span>
                        </div>
                        <table class="table-auto table">
                            <tr v-for="(item, key) in shapeArr" :key="key">
                                <td v-for="(dict, key2) in item" :key="key2" class="border-2 bg-white">
                                    {{key2}}: {{dict}}
                                </td>
                            </tr>
                        </table>
                    </div>
                </div>
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
            this._youngsMod = Number(youngsMod) * 1000; //GPa to Mpa
        }

        minArea() {
            return (FORCE_ACTING / this._ystress).toFixed(2)
        }

        bucklingSecoundMomentOfArea() {
            let pisqrd = (Math.pow(Math.PI, 2));
            let I = FORCE_ACTING * Math.pow(LENGTH, 2) / (pisqrd * Number(this._youngsMod));
            return `${(I / Math.pow(10, 6)).toFixed(2)}*10^6 mm^4`
        }

        toLst() {
            let minArea = this.minArea();
            let buckleArea = this.bucklingSecoundMomentOfArea();
            return [`${this._name}`, `${this._ystress}MPa`, `${minArea}mm^2`, `${buckleArea}`]
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
                radioType: undefined,
                shapeArr: [],
                bValue: null,
                dValue: null,
                bottomDist: null,
                radius: undefined,
                yc: 0,
            }
        },
        methods: {
            calculate() {
                let material = new Material(this.name, this.yieldStress, this.youngsMod);
                this.currentMaterials.push(material.toLst())
            },

            addToArr() {
                let shapeProperties = {};

                if (this.radioType === 'square') {
                    shapeProperties = {
                        'type': this.radioType,
                        'bValue': this.bValue,
                        'dValue': this.dValue,
                        'bottomDist': this.bottomDist,
                    }
                }
                else if(this.radioType ==='circle') {
                     shapeProperties = {
                        'type': this.radioType,
                        'radius': this.radius,
                        'bottomDist': this.bottomDist,
                    }
                }
                this.shapeArr.push(shapeProperties);
                this.radioType = null;
                this.radius = null;
                this.bottomDist = null;
                this.bValue = null;
                this.dValue = null;
            }
        },
        created() {
            let material = new Material('al 1050', 350, 70);
            this.currentMaterials.push(material.toLst())
        },

        watch: {
            shapeArr: function (){
                let shiftArea = 0;
                let areaSum = 0;
                for (let item in this.shapeArr){
                    let shape = this.shapeArr[item];
                    if (shape['type'] === 'circle') {
                        let area = Math.pow(shape['radius'],2)*Math.PI;
                        areaSum += area;
                        shiftArea += area*shape['bottomDist'];
                    }
                    else if(shape['type'] === 'square') {
                        let area = shape['bValue']*shape['dValue'];
                        areaSum += area;
                        shiftArea += area*shape['bottomDist'];
                    }
                }
                let yc = (shiftArea/areaSum).toFixed(2);
                this.yc = yc;
                return yc
            },
        },

        computed: {
            iValue: function () {
                let yc = this.yc;
                let ixx = 0;
                for (let item in this.shapeArr) {
                    let shape = this.shapeArr[item];
                    let area = 0;
                    let izz = 0;
                    if (shape['type'] === 'circle') {
                        area = Math.pow(shape['radius'],2)*Math.PI;
                        izz = Math.pow(shape['radius'],4)*Math.PI/4;
                    }
                    if(shape['type'] === 'square') {
                        area = shape['bValue']*shape['dValue'];
                        izz = Math.pow(shape['dValue'],3)*shape['bValue']
                    }
                    let shiftxsqrd = Math.pow((shape['bottomDist']- yc),2);
                    ixx += izz + area*shiftxsqrd
                }

                return ixx.toFixed()
            }
        }
    }
</script>
