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
            v-if="isShowPopup ==  true"
            @closePopup="closePopup"
            @submitForm="submitForm"
        ></PopUp>
        <CreateBill
            :type="'update'"
            :BillProp="currentBill"
            :listOrganizations="listOrganizations"
            v-if="isShowPopup === 'thêm mới'"
            @closePopup="closePopup"
            @submitForm="submitForm"
        >
        </CreateBill>
        <Header class="page-top"></Header>
        <TabLeft @closeTab="closeTab()" @openTab="openTab()"></TabLeft>
        <div class="main-content">
            <div class="page-main">
                <h1 class="page-main-title">Danh sách hóa đơn</h1>
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
                    <span class="table-assets-title div-center">
                        <p class="div-center stt-col">STT</p>
                        <p class="div-center client-name-col">
                            Tên khách hàng
                        </p>
                        <p class="div-center driver-name-col">
                            Tên tài xế
                        </p>
                        <p class="div-center time-col">
                            Thời gian
                        </p>
                        <p class="div-center oil-col">
                            Số lượng
                        </p>
                        <p class="div-center total-bill-col">
                            Giá tiền
                        </p>
                        <p class="div-center payment-col">
                            Phương thức giao dịch
                        </p>
                        <p class="div-center show-action-col tool-col"></p>
                    </span>
                    <div class="empty-icn div-center" v-show="!isHaveContent">
                        <img
                            src="../../static/icons/file-question.svg"
                            alt="file-rong"
                        />
                        <h1 class="empty-err-mess">Không có dữ liệu</h1>
                    </div>
                    <BillItem
                        v-for="(item, index) in listBills"
                        :type="'Bill'"
                        :key="index"
                        :itemProp="item"
                        :itemIndex="index + 1"
                        @showPopup="showPopup"
                        style="width: 100%"
                    ></BillItem>
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
import BillItem from '@/components/Bill/BillItem.vue';
import CreateBill from '@/components/Bill/CreateBill.vue';
export default {
    components: {
        BillItem,
        CreateBill,
    },
    data() {
        return {
            listOrganizations: [],
            listBills: [
                {
                    id: '6a386c58-dd12-44b8',
                    seller: 'abc',
                    buyer: 'Oilpicker',
                    date_issued: '12/23/2023',
                    quantity: '20',
                    totalBill: '20000',
                    payment_method: 'Momo'
                },
            ],
            currentBill: {},
            meta: [],
            currentPage: 1,
            BillID: {},
            isHaveContent: false,
            isShowPopup: '',
            showNotification: false,
            notiAction: '',
            notiObject: '',
            notiType: '',
            searchValue: '',
            timeoutId: null, // thêm biến timeoutId vào component
            selectedOption: '',
            options: ['Tất cả', 'Khoa', 'Phòng ban', 'Trung tâm'],
        };
    },
    computed: {
        pageParam() {
            return this.$route.query.page;
        },
        pageSearch() {
            return this.$route.query.search;
        },
        pageOrganization() {
            return this.$route.query.organization_id;
        },
    },
    mounted() {
        this.searchValue = this.pageSearch;
        this.organizationID = this.pageOrganization;
        if (this.pageOrganization != undefined) {
            this.fetchDetailOrganization();
        }
        //this.refreshData();
        //this.fetchListOrganization();
    },
    watch: {
        pageParam: async function () {
            this.refreshData();
        },
        listBills: {
            deep: true,
            immediate: true,
            handler(newVal) {
                if (newVal.length > 0) {
                    this.isHaveContent = true;
                } else {
                    this.isHaveContent = false;
                }
            },
        },
    },
    methods: {
        refreshData() {
            console.log(this.searchValue);
            console.log(this.organizationID);
            if (
                this.searchValue !== undefined ||
                this.organizationID !== undefined
            ) {
                this.Search();
            } else {
                this.fetchData();
            }
        },
        async downloadFile() {
            const { selectedOption, searchValue, organizationID } = this;
            let apiURL = `/Bills?pageNumber=1&pageSize=10&isConvert=true`;
            if (selectedOption) {
                apiURL += `&organization_id=${selectedOption.organizationID}`;
            }
            if (selectedOption === '' && organizationID) {
                apiURL += `&organization_id=${organizationID}`;
            }
            if (searchValue) {
                apiURL += `&searchQuery=${searchValue}`;
            }
            try {
                const response = await this.$axios({
                    method: 'get',
                    url: apiURL,
                    responseType: 'blob', // yêu cầu Axios trả về dữ liệu dạng blob (binary large object)
                });
                // Tạo đường dẫn đến tệp được tải xuống
                const url = window.URL.createObjectURL(
                    new Blob([response.data])
                );
                // Tạo một thẻ a để kích hoạt tải xuống tệp
                const link = document.createElement('a');
                link.href = url;
                link.setAttribute('download', 'SoTheoDoiPhong.xlsx');
                document.body.appendChild(link);
                link.click();
                // Xóa đối tượng thẻ a để tránh hiển thị thừa trên trang
                document.body.removeChild(link);
                this.notiAction = 'Export';
                this.notiObject = 'file';
                this.notiType = 'thành công';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
            } catch (error) {
                this.notiAction = 'Export';
                this.notiObject = 'file';
                this.notiType = 'thất bại';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
            }
        },
        async fetchData() {
            try {
                const response = await this.$axios.get(
                    `/Bills?pageNumber=${this.currentPage}&pageSize=10`
                );
                this.listBills = response.data.data;
                this.meta = response.data.meta;
                console.log(this.meta);
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
            this.currentPage = this.pageParam;
            try {
                const {
                    currentPage,
                    selectedOption,
                    searchValue,
                    organizationID,
                } = this;
                let url = `/Bills?pageNumber=${currentPage}&pageSize=10`;
                if (selectedOption) {
                    url += `&organization_id=${selectedOption.organizationID}`;
                }
                if (selectedOption === '' && organizationID) {
                    url += `&organization_id=${organizationID}`;
                }
                if (searchValue) {
                    url += `&searchQuery=${searchValue}`;
                }
                const {
                    data: { data, meta },
                } = await this.$axios.get(url);
                this.listBills = data;
                this.meta = meta;
                console.log(this.listBills);
                // Lưu trạng thái của selectedOption và searchValue vào URL của trang web
                const query = {};
                if (selectedOption) {
                    query.organization_id = selectedOption.organizationID;
                }
                if (selectedOption === '') {
                    query.organization_id = organizationID;
                }
                if (searchValue) {
                    query.search = searchValue;
                }
                this.$router.push({
                    path: `/Bills?page=${currentPage}`,
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
        async addBill(Bill) {
            try {
                await this.$axios.post(`/Bills`, {
                    BillID: Bill.BillID,
                    BillName: Bill.BillName,
                    organizationID: Bill.organizationID.organizationID,
                });
                this.fetchData();
                this.notiAction = 'Thêm mới';
                this.notiObject = 'tổ chức';
                this.notiType = 'thành công';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = '';
                }, 3000);
            } catch (error) {
                this.notiAction = 'Thêm mới';
                this.notiObject = 'tổ chức';
                this.notiType = 'thất bại';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
                console.log(error);
            }
        },
        async updateBill(Bill) {
            try {
                await this.$axios.put(`/Bills/${Bill.BillID}`, {
                    BillID: Bill.BillID,
                    BillName: Bill.BillName,
                    organizationID: Bill.organizationID.organizationID,
                });
                this.fetchData();
                this.notiAction = 'Cập nhật';
                this.notiObject = 'phòng';
                this.notiType = 'thành công';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
            } catch (error) {
                this.notiAction = 'Cập nhật';
                this.notiObject = 'phòng';
                this.notiType = 'thất bại';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = '';
                }, 3000);
                console.log(error);
            }
        },
        async deleteAsset() {
            try {
                await this.$axios.delete(`/Bills/${this.BillID}`);
                this.notiAction = 'Xóa';
                this.notiObject = 'phòng';
                this.notiType = 'thành công';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
                this.fetchData();
            } catch (error) {
                this.notiAction = 'Xóa';
                this.notiObject = 'phòng';
                this.notiType = 'thất bại';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
                console.log(error);
            }
        },
        async disposeAsset() {
            try {
                await this.$axios.post(`/Bills/${this.BillID}`);
                this.notiAction = 'Thanh lý';
                this.notiObject = 'phòng';
                this.notiType = 'thành công';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = '';
                }, 3000);
                this.fetchData();
            } catch (error) {
                this.notiAction = 'Thanh lý';
                this.notiObject = 'phòng';
                this.notiType = 'thất bại';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
                console.log(error);
            }
        },
        async fetchListOrganization() {
            try {
                const response = await this.$axios.get(`/organizations`);
                this.listOrganizations = response.data.data;
                console.log(this.listOrganizations);
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
        async fetchDetailOrganization() {
            try {
                await this.$axios
                    .get(`/organizations/${this.organizationID}`)
                    .then((res) => {
                        this.selectedOption = res['data']['data'];
                        console.log(this.selectedOption);
                    });
            } catch (error) {
                this.setNotification('Tải', 'dữ liệu', 'thất bại');
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
                console.log(error);
            }
        },
        async fetchDetail(id) {
            try {
                await this.$axios.get(`/Bills/${id}`).then((res) => {
                    this.currentBill = res['data']['data'];
                    console.log(this.currentBill);
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
            if (action == 'thêm mới') {
                this.fetchDetail(id);
                this.isShowPopup = action;
            } else if (action == 'xuất file') {
                this.notiObject = object;
                this.isShowPopup = true;
            } else {
                this.isShowPopup = true;
                this.BillID = id;
                console.log(id);
            }
            this.notiAction = action;
        },
        submitForm(action, Bill) {
            console.log(action);
            this.isShowPopup = false;
            if (action === 'xóa') {
                this.deleteAsset();
            } else if (action === 'thanh lý') {
                this.disposeAsset();
            } else if (action === 'thêm mới') {
                if (Bill.BillID) {
                    this.updateBill(Bill);
                } else {
                    this.addBill(Bill);
                }
            } else {
                this.downloadFile();
            }
        },
        closePopup() {
            this.isShowPopup = '';
            this.currentBill = {};
        },
        goToIndexPage() {
            const query = {};
            query.page = this.currentPage;
            if (this.selectedOption) {
                query.status = this.selectedOption;
            }
            if (this.searchValue) {
                query.search = this.searchValue;
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
.multiselect {
    width: 320px;
}
.stt-col{
    width: 5%;
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
.payment-col{
    width: 10%;
}
.tool-col{
    width: 5%;
}
</style>
