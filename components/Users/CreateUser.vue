<template>
    <div class="popup-container div-center">
        <Notification
            :type="'cảnh báo'"
            :warning="'Lỗi nhập liệu'"
            v-if="showNotification == 'empty'"
        ></Notification>
        <Notification
            :type="'cảnh báo'"
            :warning="'Xác nhận mật khẩu không khớp'"
            v-if="showNotification == 'password'"
        ></Notification>
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
                    v-if="JSON.stringify(userProp) === '{}'"
                >
                    Thêm tài khoản
                </h1>
                <h1 class="popup-title" v-else>Cập nhật tài khoản</h1>
                <div class="form-container">
                    <div class="device-id form-col">
                        <p class="form-label">
                            Họ và tên <small style="color: #c7422e">*</small>
                        </p>
                        <input
                            type="text"
                            class="form-inp"
                            v-model="currentUser.fullname"
                            v-validate="'required|min:1|max:30'"
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
                            Địa chỉ
                            <small style="color: #c7422e">*</small>
                        </p>
                        <input
                            type="text"
                            class="form-inp"
                            v-model="currentUser.address"
                            v-validate="'required|min:1|max:30'"
                            :class="{
                                input: true,
                                'is-danger': errors.has('Địa chỉ'),
                            }"
                            name="Địa chỉ"
                        />
                        <span
                            v-show="errors.has('Địa chỉ')"
                            class="err"
                            >{{ errors.first('Địa chỉ') }}</span
                        >
                    </div>
                    <div class="device-id form-col">
                        <p class="form-label">
                            Số điện thoại <small style="color: #c7422e">*</small>
                        </p>
                        <input
                            type="number"
                            autocomplete="off"
                            class="form-inp"
                            v-model="currentUser.phonenumber"
                            v-validate="'required|min:1|max:30'"
                            :class="{
                                input: true,
                                'is-danger': errors.has('Số điện thoại'),
                            }"
                            name="Số điện thoại"
                        />
                        <span v-show="errors.has('Số điện thoại')" class="err">{{
                            errors.first('Số điện thoại')
                        }}</span>
                    </div>
                    <div class="device-id form-col">
                        <p class="form-label">
                            Email <small style="color: #c7422e">*</small>
                        </p>
                        <input
                            type="text"
                            autocomplete="off"
                            class="form-inp"
                            v-model="currentUser.email"
                            v-validate="'required|min:1|max:30|password'"
                            :class="{
                                input: true,
                                'is-danger': errors.has('Email'),
                            }"
                            name="Email"
                        />
                        <span v-show="errors.has('Email')" class="err">{{
                            errors.first('Email')
                        }}</span>
                    </div>
                    <div class="status form-col">
                        <p class="form-label">
                            Ngân hàng
                            <small style="color: #c7422e">*</small>
                        </p>
                        <multiselect
                            class="multiselect"
                            :options="listbanks"
                            label="short-name"
                            v-model="currentUser.a"
                            placeholder="Chọn chức vụ"
                            v-validate="'required'"
                            :class="{
                                input: true,
                                'is-danger': errors.has('Ngân hàng'),
                            }"
                            name="Ngân hàng"
                        ></multiselect>
                        <span v-show="errors.has('Ngân hàng')" class="err">{{
                            errors.first('Ngân hàng')
                        }}</span>
                    </div>
                    <div class="device-id form-col">
                        <p class="form-label">
                            Số tài khoản <small style="color: #c7422e">*</small>
                        </p>
                        <input
                            type="text"
                            autocomplete="off"
                            class="form-inp"
                            v-model="currentUser.bankNumber"
                            v-validate="'required|min:1|max:30|password'"
                            :class="{
                                input: true,
                                'is-danger': errors.has('Số tài khoản'),
                            }"
                            name="Số tài khoản"
                        />
                        <span v-show="errors.has('Số tài khoản')" class="err">{{
                            errors.first('Số tài khoản')
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
    props: ['type', 'content', 'userProp'],
    data() {
        return {
            currentUser: {},
            isShowPass: false,
            isShowConfirm: false,
            password: '',
            checkBtn: false,
            showNotification: false,
            listbanks:[]
        };
    },
    watch: {
        userProp(newValue) {
            this.currentUser = newValue;
            this.password = '';
            this.currentUser.password = '';
        },
    },
    methods: {
        async submitForm() {
            const result = await this.$validator.validateAll();
            if (result && this.currentUser.password === this.password) {
                this.$emit('submitForm', 'thêm mới', this.currentUser);
                console.log(this.currentUser);
            } else if (this.currentUser.password !== this.password) {
                this.showNotification = 'password';
                setTimeout(() => {
                    this.showNotification = '';
                }, 3000);
            } else {
                this.showNotification = 'empty';
                setTimeout(() => {
                    this.showNotification = '';
                }, 3000);
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
    top: 10%;
    padding: 32px 24px 32px 24px;
    width: 550px;
    flex-direction: column;
    justify-content: flex-start;
    gap: 32px;
}
.popup-content {
    position: relative;
    max-height: 90%;
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
    font-size: 20px;
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

.note {
    grid-area: note;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    gap: 8px;
    height: 70%;
}
.form-container {
    width: 100%;
    display: flex;
    flex-direction: column;
    gap: 16px;
}

.form-col {
    position: relative;
    height: 68px;
    width: 100%;
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

.eye-icn {
    width: 16px;
    position: absolute;
    right: 20px;
    top: 37px;
    cursor: pointer;
}
</style>
