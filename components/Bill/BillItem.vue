<template>
    <div class="main" :class="{ evenLine: itemIndex % 2 === 0 }">
        <div class="item div-center">
            <p
                class="div-center stt-col"
                @click="$router.push(`/assets?page=1&Bill_id=${itemProp.BillID}`)"
            >
                {{ itemIndex }}
            </p>
            <p
                class="div-center client-name-col"
                @click="$router.push(`/assets?page=1&Bill_id=${itemProp.BillID}`)"
            >
                {{ itemProp.seller }}
            </p>
            <p
                class="div-center driver-name-col"
                @click="$router.push(`/assets?page=1&Bill_id=${itemProp.BillID}`)"
            >
                {{ itemProp.buyer }}
            </p>
            <p
                class="div-center time-col"
                @click="$router.push(`/assets?page=1&Bill_id=${itemProp.BillID}`)"
            >
                {{ itemProp.date_issued }}
            </p>
            <p
                class="div-center oil-col"
                @click="$router.push(`/assets?page=1&Bill_id=${itemProp.BillID}`)"
            >
                {{ itemProp.quantity }}
            </p>
            <p
                class="div-center total-bill-col"
                @click="$router.push(`/assets?page=1&Bill_id=${itemProp.BillID}`)"
            >
                {{ itemProp.totalBill }}
            </p>
            <p
                class="div-center payment-col"
                @click="$router.push(`/assets?page=1&Bill_id=${itemProp.BillID}`)"
            >
                {{ itemProp.payment_method }}
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
                        $emit(
                            'showPopup',
                            'xóa',
                            'phòng',
                            itemProp.BillID
                        )
                    "
                    @dispose="
                        $emit(
                            'showPopup',
                            'thanh lý',
                            'phòng',
                            itemProp.BillID
                        )
                    "
                    @update="
                        $emit(
                            'showPopup',
                            'thêm mới',
                            'phòng',
                            itemProp.BillID
                        )
                    "
                    @cancel_dispose="
                        $emit(
                            'showPopup',
                            'hủy thanh lý',
                            'phòng',
                            itemProp.BillID
                        )
                    "
                ></Tooltip>
            </span>
        </div>
    </div>
</template>

<script>
export default {
    props: ['type', 'itemProp', 'itemIndex'],
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
.id-col{
    width: 10%;
}
.client-name-col{
    width: 20%;
}
.driver-name-col{
    width: 20%;
}
.time-col{
    width: 15%;
}
.oil-col{
    width: 15%;
}
.total-bill-col{
    width: 10%;
}
.tool-col{
    width: 5%;
}
.payment-col{
    width: 10%;
}
</style>
