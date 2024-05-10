<template>
    <component :is="hiddenBlockquote ? 'div' : 'blockquote'" :class="className">
        <template v-if="isObject(data) && data">
            <button
                type="button"
                class="braces"
                padding="none"
                @click="show = !show"
            >
                {{ "#" }}
                <template v-if="!hiddenBlockquote">{{
                    title ? title : ""
                }}</template>

                <template v-if="Array.isArray(data)">{{
                    show ? " [" : " [..."
                }}</template>
                <template v-else>{{ show ? " {" : " {..." }}</template>
            </button>
            <div v-show="show">
                <div
                    class="item"
                    v-for="(value, key, i) in data"
                    v-bind:key="i"
                >
                    <span class="keyname">{{ key }}:</span>
                    <Dump :data="value" :hidden-blockquote="true"></Dump>
                </div>
            </div>
            <span class="braces close">
                <template v-if="Array.isArray(data)">{{ "]" }}</template>
                <template v-else>{{ "}" }}</template>
            </span>
        </template>
        <template v-else-if="data">
            <span
                :class="{
                    is_string: checkTypeOf(data, 'string'),
                    is_number: checkTypeOf(data, 'number'),
                    is_boolean: checkTypeOf(data, 'boolean'),
                }"
                >{{ checkTypeOf(data, "string") ? `"${data}"` : data }}</span
            >
        </template>
        <template v-else-if="checkTypeOf(data, 'undefined')">
            <span class="is_null">undefined</span>
        </template>
        <template v-else-if="typeof data == 'boolean'">
            <span class="is_boolean">{{data}}</span>
        </template>
        <template v-else>
            <span class="is_null">null</span>
        </template>
    </component>
</template>
<script lang="ts" setup>
import { computed, defineProps, ref } from "vue";

const props = defineProps(["data", "hiddenBlockquote", "title", "fixed"]);

const show = ref(false);

const isObject = (value: unknown) => {
    return ["object"].includes(typeof value);
};

const checkTypeOf = (value: unknown, typeName: string) => {
    return String(typeof value) === typeName;
};

const className = computed(() => {
    return props.fixed && !props.hiddenBlockquote ? "is_fixed" : "not_fixed";
});
</script>
<style scoped>
blockquote {
    max-height: 50vh;
    overflow-y: auto;
    background-color: black;
    color: white;
    padding: 15px;
    margin: 10px 0;
}

blockquote.is_fixed {
    margin-bottom: 0;
    position: fixed;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 9999;
}

.item {
    display: flex;
    gap: 5px;
    padding-left: 35px;
}

.braces {
    background-color: transparent;
    padding: 0;
    border: none;
    color: rgb(14, 172, 245);
    cursor: pointer;
}

.keyname {
    color: palevioletred;
}

.is_number {
    color: green;
}

.is_string {
    color: orange;
}

.is_boolean {
    color: purple;
}

.is_null {
    color: blue;
}
</style>
