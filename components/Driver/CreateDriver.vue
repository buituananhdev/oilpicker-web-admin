<template>
    <div class="popup-container div-center">
        <div class="overlay" @click="closePopup()"></div>
        <div class="popup-form div-center">
            <div class="popup-content div-center">
                <img
                    class="close-icn"
                    src="../../static/icons/close.svg"
                    alt=""
                    @click="closePopup()"
                />
                <h1
                    class="popup-title"
                    v-if="JSON.stringify(assetProp) === '{}'"
                >
                    Thêm tài xế
                </h1>
                <h1 class="popup-title" v-else>Cập nhật tài xế</h1>
                <div class="form-container">
                    <div class="asset-name form-col">
                        <p class="form-label">
                            Họ và tên <small style="color: #c7422e">*</small>
                        </p>
                        <input
                            type="text"
                            class="form-inp"
                            v-model="currentAsset.assetName"
                            v-validate="'required|min:1|max:40'"
                            :class="{
                                input: true,
                                'is-danger': errors.has('Họ và tên'),
                            }"
                            name="Họ và tên"
                        />
                        <span v-show="errors.has('Họ và tên')" class="err">{{
                            errors.first('Họ và tên')
                        }}</span>
                    </div>
                    <div class="device-id form-col">
                        <p class="form-label">
                            Tên đăng nhập <small style="color: #c7422e">*</small>
                        </p>
                        <input
                            type="text"
                            class="form-inp"
                            v-model="currentAsset.deviceID"
                            v-validate="'required|min:1|max:30'"
                            :class="{
                                input: true,
                                'is-danger': errors.has('Tên đăng nhập'),
                            }"
                            name="Tên đăng nhập"
                        />
                        <span v-show="errors.has('Tên đăng nhập')" class="err">{{
                            errors.first('Tên đăng nhập')
                        }}</span>
                    </div>
                    
                </div>
            </div>
            <span class="button-box">
                <button class="btn cancel-btn div-center" @click="closePopup()">
                    Hủy
                </button>
                <button
                    class="btn submit-btn div-center"
                    type="submit"
                    @click="submitForm()"
                >
                    Xác nhận
                </button>
            </span>
        </div>
    </div>
</template>

<script>
export default {
    props: ['type', 'content', 'assetProp'],
    data() {
        return {
            currentAsset: {},
            status: "",
            listBills: [],
            checkBtn: false,
            listStatus: [
                'Hoạt động tốt',
                'Hư hỏng, cần được sửa chữa',
                'Đang bảo dưỡng',
            ],
        };
    },
    created() {
        this.fetchBill();
    },
    watch: {
        assetProp(newValue) {
            this.currentAsset = newValue;
            this.status = newValue.status;
        },
        status(newVal) {
            this.currentAsset.status =  this.status;
            console.log(this.currentAsset);
        }
    },
    methods: {
        async fetchBill() {
            try {
                const response = await this.$axios.get(`/Bills`);
                this.listBills = response.data.data.map((Bill) => Bill.BillID);
            } catch (error) {
                console.log(error);
            }
        },
        async submitForm() {
            const result = await this.$validator.validateAll();
            if (result) {
                this.$emit('submitForm', 'thêm mới', this.currentAsset);
                console.log(this.currentAsset);
            }
        },
        closePopup() {
            this.$emit('closePopup');
        },
    },
};
</script>
<style scoped src="../../static/css/popup.css"></style>
<style scoped>
.popup-form {
    top: 5%;
    width: 680px;
    padding: 32px 24px 32px 24px;
    flex-direction: column;
    justify-content: flex-start;
    gap: 32px;
}
.popup-content {
    position: relative;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    gap: 32px;
}
.button-box {
    height: 10%;
    width: 100%;
}
.popup-title {
    font-size: 24px;
    font-weight: 600;
}
.close-icn {
    cursor: pointer;
    position: absolute;
    width: 15px;
    height: 15px;
    right: 5px;
    transition: 0.2s ease;
}
.close-icn:hover {
    transform: rotate(90deg);
}

.form-container {
    width: 100%;
    display: flex;
    flex-direction: column;
    gap: 16px;
}

.device-id {
    grid-area: device-id;
}

.asset-name {
    grid-area: asset-name;
}

.quantity {
    grid-area: quantity;
}

.Bill-name {
    grid-area: Bill-name;
}

.technicalSpecification {
    grid-area: technicalSpecification;
}

.total {
    grid-area: total;
}

.status {
    grid-area: status;
}

.note {
    grid-area: note;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    gap: 8px;
    height: 70%;
}

.form-col {
    max-height: 66px;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    gap: 8px;
}
.multiselect {
    width: 100%;
    height: 100%;
}
.form-inp {
    width: 100%;
    height: 100%;
    padding: 6px 12px;
    border: solid 0.5px #dfe0eb;
    border-radius: 6px;
    cursor: pointer;
}
input {
    height: 100%;
}

.form-inp:focus {
    outline-width: 0;
}

textarea {
    resize: none;
    height: 120px;
}
.err {
    font-size: 12px;
    display: flex;
    justify-content: flex-start;
    color: #ff4433;
}
</style>
