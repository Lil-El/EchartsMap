<template>
    <div id="app">
        <div id="map-chart" ref="charts"></div>
    </div>
</template>

<script>
import * as echarts from "echarts";
import chinaGeoJSON from "@/assets/china.json";
import shanxiGeoJSON from "@/assets/shanxi.json";
import taiyuanGeoJSON from "@/assets/taiyuan.json";
export default {
    data() {
        return {
            curType: "china",
        };
    },
    mounted() {
        this.getMapData(chinaGeoJSON);
    },
    methods: {
        getMapData(geoJson) {
            let mapData = [],
                pointData = [];
            geoJson.features.forEach((item) => {
                if (item.properties.adcode == 100000) return void 0;
                let value = Math.random() * 3000;
                mapData.push({
                    name: item.properties.name,
                    value: value,
                    cityCode: item.properties.adcode,
                });
                pointData.push({
                    name: item.properties.name,
                    value: [
                        item.properties.center[0],
                        item.properties.center[1],
                        value,
                    ],
                    cityCode: item.properties.adcode,
                });
            });
            mapData = mapData.sort(function (a, b) {
                return b.value - a.value;
            });
            this.initCharts(geoJson, mapData, pointData);
        },
        echartsMapClick(data) {
            if (data.name == "山西省") {
                this.curType = "shanxi";
                return this.getMapData(shanxiGeoJSON);
            } else if (data.name == "太原市") {
                this.curType = "taiyuan";
                return this.getMapData(taiyuanGeoJSON);
            }
        },
        initCharts(geoJson, mapData, pointData) {
            const charts = echarts.init(this.$refs["charts"], "dark");

            var xData = [],
                yData = [];
            var min = mapData[mapData.length - 1].value;
            var max = mapData[0].value;
            if (mapData.length === 1) {
                min = 0;
            }
            mapData.forEach((c) => {
                xData.unshift(
                    c.name.replace(
                        /(省|市|自治区|回族|维吾尔|壮族|特别行政区)/g,
                        ""
                    )
                );
                yData.unshift(c.value);
            });

            const option = {
                // 背景颜色
                backgroundColor: "#0e0e29",
                timeline: {
                    data: ["2020", "2021", "2022"],
                    axisType: "category",
                    autoPlay: true,
                    playInterval: 5000,
                    left: "10%",
                    right: "10%",
                    bottom: "2%",
                    width: "80%",
                    label: {
                        normal: {
                            textStyle: {
                                color: "rgb(179, 239, 255)",
                            },
                        },
                        emphasis: {
                            textStyle: {
                                color: "#fff",
                            },
                        },
                    },
                    currentIndex: 0,
                    symbolSize: 10,
                    lineStyle: {
                        color: "#8df4f4",
                    },
                    checkpointStyle: {
                        borderColor: "#8df4f4",
                        color: "#53D9FF",
                        borderWidth: 2,
                    },
                    controlStyle: {
                        showNextBtn: true,
                        showPrevBtn: true,
                        normal: {
                            color: "#53D9FF",
                            borderColor: "#53D9FF",
                        },
                        emphasis: {
                            color: "rgb(58,115,192)",
                            borderColor: "rgb(58,115,192)",
                        },
                    },
                },
                grid: [
                    {
                        show: false,
                        right: "21%",
                        top: "12%",
                        bottom: "8%",
                        containLabel: true,
                        width: "15%",
                    },
                    {
                        show: false,
                        right: "18.5%", //调整中间文字位置
                        top: "14%", //使中间文字对齐
                        bottom: "8%",
                        width: "0%",
                    },
                    {
                        show: false,
                        right: "2%",
                        top: "12%",
                        bottom: "8%",
                        containLabel: true,
                        width: "15%",
                    },
                ],
                legend: {
                    show: true,
                    icon: "roundRect",
                    itemWidth: 25,
                    itemHeight: 15,
                    itemGap: 9,
                    bottom: "10",
                    right: "20",
                    textStyle: {
                        fontSize: 14,
                        color: "#b3efff",
                    },
                    data: ["单数", "销售额"],
                },
                // 提示浮窗样式
                tooltip: {
                    show: true,
                    trigger: "item",
                    alwaysShowContent: false,
                    backgroundColor: "#0C121C",
                    borderColor: "rgba(0, 0, 0, 0.16);",
                    hideDelay: 100,
                    triggerOn: "mousemove",
                    enterable: true,
                    textStyle: {
                        color: "#DADADA",
                        fontSize: "12",
                        width: 20,
                        height: 30,
                        overflow: "break",
                    },
                    showDelay: 100,
                },
                // 数据控制条配置
                visualMap: [
                    {
                        type: "continuous",
                        min: min,
                        max: max,
                        left: "3%",
                        bottom: "5%",
                        calculable: true,
                        seriesIndex: [0],
                        inRange: {
                            color: ["#24CFF4", "#2E98CA", "#1E62AC"],
                        },
                        textStyle: {
                            color: "#24CFF4",
                        },
                    },
                ],
                //* 工具栏
                toolbox: {
                    feature: {
                        // restore: {
                        //     show: true,
                        // },
                        saveAsImage: {
                            name: "China",
                        },
                    },
                    iconStyle: {
                        normal: {
                            borderColor: "#1990DA", //右上角下载的颜色
                        },
                    },
                    top: 15,
                    right: 35,
                },
                // 地图配置
                geo: {
                    map: this.curType == "china" ? "china" : "map",
                    zoom: 1.1,
                    roam: true,
                    left: "10%",
                    top: "15%",
                    label: {
                        // 通常状态下的样式
                        normal: {
                            show: true,
                            textStyle: {
                                color: "#f9f9f9",
                            },
                        },
                        // 鼠标放上去的样式
                        emphasis: {
                            textStyle: {
                                color: "#F75A00",
                            },
                        },
                    },
                    // 地图区域的样式设置
                    itemStyle: {
                        normal: {
                            areaColor: "#24CFF4",
                            borderColor: "#53D9FF",
                            borderWidth: 1.3,
                            shadowBlur: 15,
                            shadowColor: "#3a73c0",
                            shadowOffsetX: 7,
                            shadowOffsetY: 6,
                        },
                        emphasis: {
                            areaColor: "#8dd7fc",
                            borderWidth: 1.6,
                            shadowBlur: 25,
                        },
                    },
                },
                xAxis: [
                    {
                        type: "value",
                        inverse: true, //是否是反向坐标轴。
                        scale: true,
                        position: "top",
                        boundaryGap: false,
                        splitLine: {
                            show: false,
                        },
                        axisLine: {
                            show: true,
                            lineStyle: {
                                color: "#455B77",
                            },
                        },
                        axisTick: {
                            show: true,
                        },
                        axisLabel: {
                            show: true,
                            lineStyle: {
                                color: "rgba(255,255,255,0.2)",
                            },
                            margin: 2,
                            textStyle: {
                                color: "#c0e6f9",
                            },
                        },
                    },
                    {
                        gridIndex: 1,
                        show: false,
                    },
                    {
                        gridIndex: 2,
                        type: "value",
                        inverse: false, //是否是反向坐标轴。
                        scale: true,
                        position: "top",
                        boundaryGap: false,
                        splitLine: {
                            show: false,
                        },
                        axisLine: {
                            show: true,
                            lineStyle: {
                                color: "#455B77",
                            },
                        },
                        axisTick: {
                            show: true,
                        },
                        axisLabel: {
                            show: true,
                            lineStyle: {
                                color: "rgba(255,255,255,0.2)",
                            },
                            margin: 2,
                            textStyle: {
                                color: "#c0e6f9",
                            },
                        },
                    },
                ],
                yAxis: [
                    {
                        type: "category",
                        inverse: false,
                        position: "right",
                        nameGap: 16,
                        axisLine: {
                            show: true,
                            lineStyle: {
                                color: "#455B77",
                            },
                        },
                        axisTick: {
                            show: false,
                        },
                        axisLabel: {
                            show: false,
                        },
                        data: ["山西", "陕西", "辽宁"],
                    },
                    {
                        gridIndex: 1,
                        type: "category",
                        inverse: false,
                        position: "center",
                        nameGap: 16,
                        axisLine: {
                            show: false,
                        },
                        axisTick: {
                            show: false,
                        },
                        axisLabel: {
                            show: true,
                            textStyle: {
                                color: "#c0e6f9",
                                fontSize: 12,
                                align: "center",
                            },
                        },
                        data: ["山西", "陕西", "辽宁"],
                    },
                    {
                        gridIndex: 2,
                        type: "category",
                        inverse: false,
                        position: "left",
                        nameGap: 16,
                        axisLine: {
                            show: true,
                            lineStyle: {
                                color: "#455B77",
                            },
                        },
                        axisTick: {
                            show: false,
                        },
                        axisLabel: {
                            show: false,
                        },
                        data: ["山西", "陕西", "辽宁"],
                    },
                ],
                series: [
                    {
                        //最外层鼠标经过显示的黑框
                        name: "年销售额度",
                        type: "map",
                        geoIndex: 0,
                        map: this.curType == "china" ? "china" : "map",
                        roam: true,
                        zoom: 1.3,
                        label: {
                            normal: {
                                show: true,
                                position: "right",
                                distance: 5,
                                color: "white",
                                backgroundColor: "#1D3039",
                                padding: 10,
                                borderRadius: 20,
                            },
                            emphasis: {
                                show: false,
                            },
                        },
                        data: mapData,
                    },
                    {
                        name: "散点",
                        type: "effectScatter",
                        coordinateSystem: "geo",
                        rippleEffect: {
                            brushType: "fill",
                        },
                        itemStyle: {
                            normal: {
                                color: "#F4E925",
                                shadowBlur: 10,
                                shadowColor: "#333",
                            },
                        },
                        data: pointData,
                        symbolSize: function (val) {
                            let value = val[2];
                            if (value == max) {
                                return 27;
                            }
                            return 10;
                        },
                        showEffectOn: "render", //加载完毕显示特效
                    },
                    {
                        name: "单数",
                        type: "bar",
                        barGap: "-100%",
                        barCategoryGap: "60%",
                        stack: "left",
                        label: {
                            show: true,
                            fontSize: 10,
                            distance: 10,
                            color: "#fff",
                            position: "left", //inside|right
                            formatter: (params) => {
                                return params.value.toFixed(2);
                            },
                        },
                        itemStyle: {
                            normal: {
                                barBorderRadius: 30,
                                color: new echarts.graphic.LinearGradient(
                                    0,
                                    1,
                                    1,
                                    1,
                                    [
                                        { offset: 0, color: "#EB3B5A" },
                                        { offset: 1, color: "#FE9C5A" },
                                    ]
                                ),
                            },
                            emphasis: {
                                show: false,
                            },
                        },
                        data: [200, 609, 1900],
                    },
                    {
                        name: "销售额",
                        type: "bar",
                        barGap: "-100%",
                        barCategoryGap: "60%",
                        stack: "right",
                        xAxisIndex: 2,
                        yAxisIndex: 2,
                        label: {
                            show: true,
                            fontSize: 10,
                            distance: 10,
                            color: "#fff",
                            position: "right", //inside|right
                            formatter: (params) => {
                                return params.value.toFixed(2);
                            },
                        },
                        itemStyle: {
                            normal: {
                                barBorderRadius: 30,
                                color: new echarts.graphic.LinearGradient(
                                    0,
                                    1,
                                    1,
                                    1,
                                    [
                                        { offset: 0, color: "#395CFE" },
                                        { offset: 1, color: "#2EC7CF" },
                                    ]
                                ),
                            },
                            emphasis: {
                                show: false,
                            },
                        },
                        data: [200, 609, 1900],
                    },
                ],
            };
            // 地图注册，第一个参数的名字必须和option.geo.map一致
            echarts.registerMap(
                this.curType == "china" ? "china" : "map",
                geoJson
            );
            charts.setOption(option);

            //点击前解绑，防止点击事件触发多次
            charts.off("click");
            charts.on("click", this.echartsMapClick);
            //监听时间切换事件
            // charts.off("timelinechanged");
            // charts.on("timelinechanged", (params) => {
            //     this.currentIndex = params.currentIndex;
            //     this.getMapData();
            // });
        },
    },
};
</script>

<style>
body {
    padding: 0;
    margin: 0;
}
#app {
    width: 100vw;
    height: 100vh;
}
#map-chart {
    width: 100vw;
    height: 100vh;
}
</style>
