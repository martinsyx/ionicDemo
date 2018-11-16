#这是对应的数据格式
velData=[
    {
        date: "2018-11-01",
        time: [
            1,1,1,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1
        ],
        budget: 30
    }, {
        date: "2018-11-02",
        time: [
            0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1
        ],
        budget: 30
    }, {
        date: "2018-11-03",
        time: [
            0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1
        ],
        budget: 30
    }, {
        date: "2018-11-04",
        time: [
            0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1
        ],
        budget: 30
    }, {
        date: "2018-11-05",
        time: [
            0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1
        ],
        budget: 30
    }, {
        date: "2018-11-06",
        time: [
            0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1
        ],
        budget: 30
    }, {
        date: "2018-11-07",
        time: [
            0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1
        ],
        budget: 30
    }, {
        date: "2018-11-08",
        time: [
            0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1
        ],
        budget: 30
    }
]

#使用方法

import advantage from 'component/datePickHour/datePickHour.vue'

export default {
    components: {
        advantage,
    },
    data(){
        return {
            velData:velData,
        }
    },
}

<template>
<div>
    <advantage v-model='velData' :isview='false'></advantage>
</div>
<template>


