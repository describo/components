<template>
    <div class="flex flex-col describo-property-type-select">
        <el-select
            v-if="data.hasValidValues"
            class="w-full"
            v-model="data.internalValue"
            :placeholder="$t('select')"
            filterable
            @change="save"
        >
            <el-option
                v-for="(value, idx) in props.definition.values"
                :key="idx"
                :label="value"
                :value="value"
            >
            </el-option>
        </el-select>
        <div v-else class="text-xs text-gray-700">
            {{ $t('invalid_select_value') }}
        </div>
    </div>
</template>

<script setup>
import { ElSelect, ElOption } from "element-plus";
import { reactive, watch } from "vue";
import isArray from "lodash-es/isArray";
import isString from "lodash-es/isString";
import uniq from "lodash-es/uniq";
import {$t} from '../i18n'

const props = defineProps({
    property: {
        type: String,
        required: true,
    },
    value: {
        type: String,
    },
    definition: {
        type: Object,
        required: true,
    },
});
const $emit = defineEmits(["save:property"]);
const data = reactive({
    internalValue: props.value,
    hasValidValues: verifySelectValuesAreStrings(props.definition.values),
});

watch(
    () => props.value,
    () => {
        data.internalValue = props.value;
        data.hasValidValues = verifySelectValuesAreStrings(props.definition.values);
    }
);
watch(
    () => props.definition.values,
    () => {
        data.items = [...props.definition.values];
        data.hasValidValues = verifySelectValuesAreStrings(props.definition.values);
    }
);

function save() {
    $emit("save:property", {
        property: props.property,
        value: data.internalValue,
    });
}
function verifySelectValuesAreStrings(values) {
    if (!isArray(values)) return false;
    values = values.map((v) => isString(v));
    values = uniq(values);
    return values.length === 1 && values[0] === true ? true : false;
}
</script>
