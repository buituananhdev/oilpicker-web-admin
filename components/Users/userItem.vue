<template>
    <div class="main" :class="{ evenLine: itemIndex % 2 == 0 }">
        <div class="item div-center">
            <p class="div-center stt-col">
                {{ itemIndex }}
            </p>
            <p class="div-center name-col">
                {{ itemProp.fullname }}
            </p>
            <p class="div-center email-col">
                {{ itemProp.email }}
            </p>
            <p class="div-center address-col">
                {{ itemProp.address }}
            </p>
            <p class="div-center phone-number-col">
                {{ itemProp.phonenumber }}
            </p>

            <div 
                class="div-center tool-col"
                @mouseover="showAction()"
                @mouseleave="hideAction()"
            >
                <img v-if="itemProp.username !== Username" src="../../static/icons/three-dots-vertical.svg" alt="" />
                <Tooltip
                v-if="itemProp.username !== Username"
                    class="tooltip"
                    :class="'tooltip' + itemIndex"
                    :type="type"
                    :obj="'user'"
                    @mouseover="showAction()"
                    @delete="
                        $emit('showPopup', 'xóa', 'tài khoản', itemProp.id)
                    "
                    @update="
                        $emit(
                            'showPopup',
                            'thêm mới',
                            'tài khoản',
                            itemProp.id
                        )
                    "
                ></Tooltip>
            </div >
        </div>
    </div>
</template>

<script>
export default {
    props: ['type', 'itemProp', 'itemIndex'],
    data() {
        return {
            Username: '',
        };
    },
    computed: {
        pageParam() {
            return this.$route.query.page;
        },
    },
    created() {
        //this.Username = localStorage.getItem('currentUsername');
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
}
.item p {
    font-size: 15px;
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
</style>
