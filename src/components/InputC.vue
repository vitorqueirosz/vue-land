<template>
    <label :name="name" class="label">
        {{ label }}
        <input 
            class="input"
            :name="name"
            :type="type" 
            :placeholder="placeholder" 
            :value="modelValue"
            @input="$emit('update:modelValue', $event.target.value)"
            v-bind="$attrs"
        />
    </label>
  
</template>

<script>
    export default {
        name: 'InputC',
        props: {
            name: {
                type: String,
                required: true,
            },
            placeholder: {
                type: String,
                required: true,
            },
            type: {
                type: String,
                default: 'text',
                required: true,
            },
            label: {
                type: String,
                required: true,
            },
            modelValue: {
                type: Number,
                default: 0,
            },
        }
    }
</script>

<style>
    .label {
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        margin: auto;
    }
    .input {
        border: 1px solid bisque;
        border-radius: 4px;
        background-color: #FFECC7;
        padding: 8px;
    }
</style>