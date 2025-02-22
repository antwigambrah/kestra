<template>
    <div class="status-pie" v-if="dataReady">
        <el-tooltip
            popper-class="tooltip-stats"
            placement="right"
            :persistent="false"
            :hide-after="0"
            transition=""
            :popper-class="tooltipContent === '' ? 'd-none' : 'tooltip-stats'"
        >
            <template #content>
                <span v-html="tooltipContent" />
            </template>
            <DoughnutChart ref="chartRef" :chart-data="chartData" :options="options" />
        </el-tooltip>
    </div>
</template>

<script>
    import {defineComponent, computed, ref} from "vue";
    import {DoughnutChart} from "vue-chart-3"
    import {tooltip, defaultConfig, backgroundFromState} from "../../utils/charts.js";
    import State from "../../utils/state";
    import {cssVariable} from "../../utils/global";

    export default defineComponent({
        components: {DoughnutChart},
        props: {
            data: {
                type: Object,
                required: true
            },
        },
        setup(props) {
            const chartRef = ref();
            const tooltipContent = ref("");

            const dataReady = computed(() => props.data !== undefined)

            const options = computed(() => defaultConfig({
                layout: {
                    padding: 0
                },
                spacing: 0,
                cutout: "75%",
                borderWidth: 0,
                borderColor: cssVariable("--bs-border-color"),
                hoverBorderColor: cssVariable("--bs-border-color"),
                plugins: {
                    tooltip: {
                        external: function (context) {
                            let content = tooltip(context.tooltip);
                            if (content) {
                                tooltipContent.value = content;
                            }
                        },
                    },
                }
            }))

            const chartData = computed(() => {
                let background = Object.keys(props.data.executionCounts).map(function (state) {
                    return backgroundFromState(state)
                });

                let datasets = [
                    {
                        backgroundColor: background,
                        data: Object.values(props.data.executionCounts)
                    }
                ];

                return {
                    labels: Object.keys(props.data.executionCounts),
                    datasets: datasets
                }
            });

            return {chartData, tooltipContent, chartRef, options, dataReady};
        },
    });
</script>

<style lang="scss" scoped>
@import "../../styles/_variable.scss";

.status-pie {
    div {
        height: 100px;

        @media (min-width: map-get($grid-breakpoints, "md")) {
            & {
                height: 200px;
            }
        }
    }
}

</style>