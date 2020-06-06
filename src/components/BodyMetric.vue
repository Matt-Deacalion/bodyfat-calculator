<template>
    <div class="metric">
        <div :class="{ disabled }">
            <label :for="name">{{ name }}:</label>
            <input
                :id="name"
                :name="name"
                :value="measure"
                @input="$emit('input', $event.target.value)"
                @keypress="onlyNumber"
            />
        </div>
    </div>
</template>

<script>
export default {
    name: 'BodyMetric',

    props: {
        name: {
            type: String,
            required: true,
        },

        measure: {
            type: String,
            required: true,
        },

        disabled: {
            type: Boolean,
        }
    },

    methods: {
        // prevent entering anything other than a number or `.` in fields
        onlyNumber ($event) {
            let keyCode = $event.keyCode ? $event.keyCode : $event.which;

            if ((keyCode < 48 || keyCode > 57) && keyCode !== 46)
                $event.preventDefault();
        }
    }
}
</script>

<style scoped>
    label {
        text-transform: capitalize;
    }

    .metric > div {
        position: relative;

        display: flex;
        justify-content: space-between;
    }

    .disabled::after, .disabled > * {
        opacity: .25;
    }

    .disabled::after {
        position: absolute;

        width: 100%;
        height: 100%;

        content: ' ';

        background: url('data:image/svg+xml;utf8,<svg xmlns=\'http://www.w3.org/2000/svg\' version=\'1.1\' preserveAspectRatio=\'none\' viewBox=\'0 0 100 100\'><path d=\'M100 0 L0 100 \' stroke=\'%23f04646\' stroke-width=\'4\'/><path d=\'M0 0 L100 100 \' stroke=\'%23f04646\' stroke-width=\'4\'/></svg>');
        background-size: 100% 100%;
    }
</style>
