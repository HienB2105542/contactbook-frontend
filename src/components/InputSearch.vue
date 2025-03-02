<script>
export default {
    props: {
        moduleValue: {type: String, default: ""},
    },
    emits: ["submit", "update:modeValue"],
    methods: {
        updateModeValue(e) {
            this.$emit("update:modeValue", e.target.value); 
        },

        submit(){
            this.$emit("submit");
        },
    },
};
</script>
<template>
    <div class="input-group">
        <input
        type="text"
        class="form-control"
        placeholder="Nhập thông tin cần tìm"
        :value="moduleValue"
        @input="updateModeValue"
        @keyup.enter="submit" 
        />
        <div class="input-group-append">
            <button
            class="btn btn-ontline-secondary"
            type="button"
            @click="submit"
            >
            <i class="fas fa-search"></i> Tìm kiếm
            </button>
        </div>
    </div>
</template>