<template>
    <div class="main" :class="{ evenLine: itemIndex % 2 == 0 }">
        <div class="item div-center">
            <p
                class="div-center stt-col"
                @click="pushToDetails"
            >
                {{ itemIndex }}
            </p>
            <p
                class="div-center name-col"
                @click="pushToDetails"
            >
                {{ itemProp.fullname }}
            </p>
            <p
                class="div-center email-col"
                @click="pushToDetails"
            >
                {{ itemProp.email }}
            </p>
            <p
                class="div-center address-col"
                @click="pushToDetails"
            >
                {{ itemProp.address }}
            </p>
            <p
                class="div-center phone-number-col"
                @click="pushToDetails"
            >
                {{ itemProp.phonenumber }}
            </p>
            <span
                class="div-center show-action-col tool-col"
                @mouseover="showAction()"
                @mouseleave="hideAction()"
            >
                <img src="../../static/icons/three-dots-vertical.svg" alt="" />
                
                <Tooltip
                    class="tooltip"
                    :class="'tooltip' + itemIndex"
                    :type="type"
                    @mouseover="showAction()"
                    @delete="
                        $emit('showPopup', 'xóa', 'tài xế', itemProp.assetID)
                    "
                    @update="
                        $emit('showPopup', 'thêm mới', 'tài xế', itemProp.assetID)
                    "
                ></Tooltip>
            </span>
        </div>
    </div>
</template>

<script>
export default {
    props: ['type', 'itemProp', 'itemIndex'],
    data() {
        return {
            timeFormat: '',
            moneyFormart: null,
        };
    },
    mounted() {
        let date = new Date(this.itemProp.dateDisposed); // Tạo đối tượng Date từ chuỗi thời gian
        // this.timeFormat = date.toLocaleDateString('vi-VN');
        // this.moneyFormart = this.itemProp.cost.toLocaleString('vi-VN', {
        //     style: 'currency',
        //     currency: 'VND',
        // });
    },
    computed: {
        pageParam() {
            return this.$route.query.page;
        },
    },
    methods: {
        showAction() {
            document
                .querySelector('.tooltip' + this.itemIndex)
                .classList.add('display-block');
        },
        hideAction() {
            document
                .querySelector('.tooltip' + this.itemIndex)
                .classList.remove('display-block');
        },
        pushToDetails() {
            if(this.type === 'asset') {
                this.$router.push(`/details_asset?id=${this.itemProp.assetID}`);
            }
        }
    },
};
</script>

<style scoped>
.popup {
    height: 120vh;
    top: -80px;
    left: -255px;
    right: 0;
    bottom: 0;
}
.display-block {
    display: block !important;
}
.main {
    width: 100%;
    height: 60px;
    display: flex;
    gap: 20px;
    padding: 0 32px;
}
.item {
    width: 100%;
    cursor: pointer;
}
.item p {
    font-size: 15px;
}
.item:hover p {
    color: #008cde;
    text-decoration: underline;
}
.item:hover .custom-select-trigger {
    color: #008cde;
}
.evenLine {
    background: #dfe0eb;
}
.stt-col{
    width: 5%;
}
.name-col{
    width: 25%;
}
.email-col{
    width: 20%;
}
.address-col{
    width: 20%;
}
.phone-number-col{
    width: 20%;
}

.tool-col{
    width: 5%;
}
.status-col{
    width: 10%;
}

</style>