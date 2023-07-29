<template>
    <div class="main" :class="{ evenLine: itemIndex % 2 === 0 }">
        <div class="item div-center">
            <p
                class="div-center stt-col"
                @click="$router.push(`/Bills?page=1&organization_id=${itemProp.organizationID}`)"
            >
                {{ itemIndex }}
            </p>
            <p
                class="div-center day-col"
                @click="$router.push(`/Bills?page=1&organization_id=${itemProp.organizationID}`)"
            >
                {{ itemProp.user_id }}
            </p>
            <p
            class="div-center type-col"
            @click="$router.push(`/Bills?page=1&organization_id=${itemProp.organizationID}`)"
            >
            {{ itemProp.order_type }}
            </p>
            <p
                class="div-center appoint-day-col"
                @click="$router.push(`/Bills?page=1&organization_id=${itemProp.organizationID}`)"
            >
                {{ itemProp.recurring_day_of_week }}
            </p>
            <p
                class="div-center appoint-time-col"
                @click="$router.push(`/Bills?page=1&organization_id=${itemProp.organizationID}`)"
            >
                {{ itemProp.recurring_time }}
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
                        $emit('showPopup', 'xóa', 'dầu', itemProp.organizationID)
                    "
                    @dispose="
                        $emit('showPopup', 'thanh lý', 'dầu', itemProp.organizationID)
                    "
                    @update="
                        $emit('showPopup', 'thêm mới', 'dầu', itemProp.organizationID)
                    "
                    @cancel_dispose="$emit('showPopup', 'hủy thanh lý', 'dầu', itemProp.organizationID)"
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
    width: 10%;
}
.day-col{
    width: 20%;
}
.type-col{
    width: 25%;
}
.appoint-day-col{
    width: 20%;
}
.appoint-time-col{
    width: 20%;
}
.tool-col{
    width: 5%;
}
</style>