<template>
    <div class="container">
        <Notification
            :type="notiType"
            :object="notiObject"
            :action="notiAction"
            v-if="showNotification"
        ></Notification>
        <PopUp
            class="popup"
            :type="'warning'"
            :action="notiAction"
            :object="notiObject"
            v-show="isShowPopup"
            @closePopup="closePopup"
            @submitForm="submitForm"
        ></PopUp>
        <Header class="page-top"></Header>
        <TabLeft @closeTab="closeTab()" @openTab="openTab()"></TabLeft>
        <div class="main-content">
            <div class="page-main">
                <h1 class="page-main-title">Danh sách tài khoản</h1>
                <div class="action-container">
                    <div class="search">
                        <input
                            type="text"
                            class="inp-search"
                            placeholder="Tìm kiếm..."
                            v-model="searchValue"
                            @input="onSearchInput"
                        />
                        <img
                            class="icn-search"
                            src="../../static/icons/search.svg"
                            alt=""
                        />
                    </div>
                </div>

                <div class="table-assets">
                    <div class="table-assets-title div-center">
                        <p class="div-center stt-col">STT</p>
                        <p class="div-center name-col">Họ và tên</p>
                        <p class="div-center email-col">Email</p>
                        <p class="div-center address-col">Địa chỉ</p>
                        <p class="div-center phone-number-col">Số điện thoại</p>
                        <div class="div-center tool-col"></div>
                    </div>
                    <div class="empty-icn div-center" v-show="!isHaveContent">
                        <img
                            src="../../static/icons/file-question.svg"
                            alt="file-rong"
                        />
                        <h1 class="empty-err-mess">Không có dữ liệu</h1>
                    </div>
                    <UserItem
                        v-for="(item, index) in listUsers"
                        :type="'user'"
                        :key="index"
                        :itemProp="item"
                        :itemIndex="index + 1"
                        @showPopup="showPopup"
                        style="width: 100%"
                    ></UserItem>
                </div>
                <div class="pagination">
                    <div
                        class="pagination-content div-center"
                        v-show="isHaveContent"
                    >
                        <span class="total-page div-center"
                            >Total page: {{ meta.totalPages }}</span
                        >
                        <span class="pagination-btn div-center">
                            <img
                                v-show="meta.hasPreviousPage"
                                style="cursor: pointer"
                                src="../../static/icons/chevron-left.svg"
                                alt=""
                                @click="goToPrevPage()"
                            />
                            <p
                                v-show="meta.currentPage >= 3"
                                style="cursor: context-menu"
                            >
                                ...
                            </p>
                            <p
                                v-show="meta.hasPreviousPage"
                                @click="goToPrevPage()"
                            >
                                {{ meta.currentPage - 1 }}
                            </p>
                            <p class="active-page">{{ meta.currentPage }}</p>
                            <p
                                v-show="meta.hasNextPage"
                                @click="goToNextPage()"
                            >
                                {{ meta.currentPage + 1 }}
                            </p>
                            <p
                                v-show="meta.currentPage + 1 < meta.totalPages"
                                style="cursor: context-menu"
                            >
                                ...
                            </p>
                            <img
                                v-show="meta.hasNextPage"
                                style="cursor: pointer"
                                src="../../static/icons/chevron-right.svg"
                                alt=""
                                @click="goToNextPage()"
                            />
                        </span>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import CreateUser from '@/components/Users/CreateUser.vue';
import UserItem from '@/components/Users/userItem.vue';
export default {
    components: {
        UserItem,
        CreateUser,
    },
    // async asyncData({ $axios }) {
    //     const { data } = await $axios.get(
    //         `/users?page=${1}`
    //     );
    //     return {
    //         listUsers: data.data
    //     }
    // },
    data() {
        return {
            UserID: '',
            searchValue: '',
            meta: [],
            currentPage: 1,
            isHaveContent: false,
            isShowPopup: false,
            showNotification: false,
            notiAction: '',
            notiObject: '',
            notiType: '',
            permission: '',
            listPermission: ['Quản trị viên', 'Nhân viên'],
            currentUser: {},
            listUsers: []
        };
    },
    computed: {
        pageParam() {
            return this.$route.query.page;
        },
        pageSearch() {
            return this.$route.query.search;
        },
        pagePermission() {
            return this.$route.query.permission;
        },
    },
    mounted() {
        // this.searchValue = this.pageSearch;
        this.refreshData();
    },
    watch: {
        pageParam: async function () {
            this.refreshData();
        },
        listUsers: {
            deep: true,
            immediate: true,
            handler(newVal) {
                if (newVal) {
                    this.isHaveContent = true;
                } else {
                    this.isHaveContent = false;
                }
            },
        },
    },
    methods: {
        async refreshData() {
            if (this.searchValue !== '') {
                await this.Search();
            } else {
                await this.fetchData();
            }
        },
        async fetchData() {
            try {
                const response = await this.$axios.get(
                    `/users?page=${this.currentPage}`
                );
                this.listUsers = response.data.data;
                this.meta = response.data.meta;
                console.log(this.listUsers);
            } catch (error) {
                console.log(error);
                this.notiAction = 'Tải';
                this.notiObject = 'dữ liệu';
                this.notiType = 'thất bại';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
            }
        },
        async Search() {
            console.log('search');
            this.currentPage = this.pageParam;
            try {
                const { currentPage, searchValue } = this;
                let url = `/users?page=${currentPage}`;
                if (searchValue) {
                    url += `&searchQuery=${searchValue}`;
                }
                const {
                    data: { data, meta },
                } = await this.$axios.get(url);
                this.listUsers = data;
                this.meta = meta;
                console.log(this.listUsers);
                // Lưu trạng thái của permission và searchValue vào URL của trang web
                const query = {};
                if (searchValue) {
                    query.search = searchValue;
                }
                this.$router.push({
                    path: `/users?page=${currentPage}`,
                    query,
                });
            } catch (error) {
                console.error(error);
                this.notiAction = 'Tải';
                this.notiObject = 'dữ liệu';
                this.notiType = 'thất bại';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
            }
        },
        // debounce
        onSearchInput() {
            clearTimeout(this.timeoutId); // xóa bỏ setTimeout() trước đó (nếu có)
            this.timeoutId = setTimeout(() => {
                this.Search();
            }, 700); // tạo mới setTimeout() với thời gian chờ là 700ms
        },
        async deleteUser() {
            try {
                await this.$axios.delete(`/users/${ this.id }`);
                this.notiAction = 'Xóa';
                this.notiObject = 'người dùng';
                this.notiType = 'thành công';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
                this.fetchData();
            } catch (error) {
                this.notiAction = 'Xóa';
                this.notiObject = 'người dùng';
                this.notiType = 'thất bại';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
                console.log(error);
            }
        },
        async updateUser(user) {
            try {
                await this.$axios.put(`/users/${user.userID}`, {
                    userID: user.userID,
                    username: user.username,
                    password: user.password,    
                    fullName: user.fullName,
                    userRole: user.userRole,
                });
                this.fetchData();
                this.notiAction = 'Cập nhật';
                this.notiObject = 'user';
                this.notiType = 'thành công';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
            } catch (error) {
                this.notiAction = 'Cập nhật';
                this.notiObject = 'user';
                this.notiType = 'thất bại';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = '';
                }, 3000);
                console.log(error);
            }
        },
        async fetchDetail(id) {
            try {
                await this.$axios.get(`/users/${id}`).then((res) => {
                    this.currentUser = res['data']['data'];
                    console.log(this.currentUser);
                });
            } catch (error) {
                console.log(error);
            }
        },
        closeTab() {
            document
                .querySelector('.main-content')
                .classList.add('close-collapse');
            document
                .querySelector('.main-content')
                .classList.remove('open-collapse');
            document.querySelector('.page-top').classList.add('close-collapse');
            document
                .querySelector('.page-top')
                .classList.remove('open-collapse');
        },
        openTab() {
            document
                .querySelector('.main-content')
                .classList.add('open-collapse');
            document
                .querySelector('.main-content')
                .classList.remove('close-collapse');
            document.querySelector('.page-top').classList.add('open-collapse');
            document
                .querySelector('.page-top')
                .classList.remove('close-collapse');
        },
        showPopup(action, object, id) {
            if (action == 'xóa') {
                this.id = id;
                this.isShowPopup = action;
            } else if (action == 'thêm mới') {
                this.id = id;
                this.fetchDetail(this.id)
                this.isShowPopup = action;
            }
            this.notiAction = action;
        },
        submitForm(action, user) {
            console.log(action);
            console.log(user);
            this.isShowPopup = false;
            if (action === 'xóa') {
                this.deleteUser();
            } else if (action === 'thêm mới') {
                this.updateUser(user);
            }
        },
        closePopup() {
            this.isShowPopup = '';
            this.currentUser = {};
        },
        goToIndexPage() {
            const query = {};
            query.page = this.currentPage;
            if (this.permission) {
                query.status = this.permission;
            }
            this.$router.push({
                query: query,
            });
        },
        goToNextPage() {
            this.goToIndexPage(this.currentPage++);
        },
        goToPrevPage() {
            this.goToIndexPage(this.currentPage--);
        },
    },
};
</script>

<style scoped src="../../static/css/table_assets.css"></style>
<style scoped>
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
