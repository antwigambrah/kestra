<template>
    <el-select
        :model-value="labels"
        @update:model-value="onInput"
        multiple
        filterable
        allow-create
        clearable
        :collapse-tags="collapseTags"
        default-first-option
        :persistent="false"
        :reserve-keyword="false"
        @focus="hover = true"
        @blur="hover = false"
        :placeholder="hover ? $t('label filter placeholder') : $t('labels')"
    >
        <el-option
            v-for="label in labels"
            :key="label"
            :label="label"
            :value="label"
        />
    </el-select>
</template>

<script>
    const isValidLabel = (label) => {
        return label.match(".+:.+") !== null;
    };

    const isValidLabels = (labels) => {
        return labels.every((label) => isValidLabel(label));
    };

    export default {
        props: {
            modelValue: {
                type: [Array, String],
                default: [],
                validator(value) {
                    return isValidLabels(value);
                }
            }
        },
        emits: ["update:modelValue"],
        created() {
            this.labels = this.asArrayProp(this.modelValue);
        },
        data() {
            return {
                hover: false,
                inputValue: undefined,
                labels: [],
            }
        },
        watch: {
            modelValue: {
                handler (newValue, _) {
                    this.labels = this.asArrayProp(newValue);
                }
            }
        },
        methods: {
            asArrayProp(unknownValue) {
                return (!Array.isArray(unknownValue) && unknownValue !== undefined) ? [unknownValue] : unknownValue;
            },
            onInput(value) {
                this.labels = value;
                this.$emit("update:modelValue", value)
            },
        }
    };
</script>