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
            v-show="isShowPopup == true"
            @closePopup="closePopup"
            @submitForm="submitForm"
        ></PopUp>
        <CreateDriver
            :type="'update'"
            :assetProp="currentAsset"
            v-show="isShowPopup === 'thêm mới'"
            @closePopup="closePopup"
            @submitForm="submitForm"
        >
        </CreateDriver>
        <Header class="page-top"></Header>
        <TabLeft @closeTab="closeTab()" @openTab="openTab()"></TabLeft>
        <div class="main-content">
            <div class="page-main">
                <h1 class="page-main-title">Danh sách tài xế</h1>
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
                    <!-- <multiselect
                        class="multiselect"
                        :options="options"
                        v-model="selectedOption"
                        placeholder="Trạng thái của tài xế"
                        @input="Search"
                    ></multiselect>
                    <multiselect
                        class="multiselect"
                        :options="listBills"
                        v-model="selectedBill"
                        label="BillName"
                        placeholder="Tìm kiếm hoặc chọn phòng"
                        @input="Search"
                    ></multiselect> -->
                    <div class="btn-container">
                        <!-- <button
                            class="create-btn"
                            @click="showPopup('xuất file', 'bảng dữ liệu')"
                        >
                            Xuất file excel
                        </button> -->
                        <button
                            class="create-btn"
                            @click="isShowPopup = 'thêm mới'"
                        >
                            Thêm tài xế
                        </button>
                    </div>
                </div>
                <div class="table-assets">
                    <span class="table-assets-title div-center">
                        <p class="div-center stt-col">STT</p>
                        <p class="div-center name-col">Họ và tên</p>
                        <p class="div-center email-col">Email</p>
                        <p class="div-center address-col">Địa chỉ</p>
                        <p class="div-center phone-number-col">Số điện thoại</p>
                        <div class="div-center tool-col"></div>
                    </span>
                    <div class="empty-icn div-center" v-show="!isHaveContent">
                        <img
                            src="../../static/icons/file-question.svg"
                            alt="file-rong"
                        />
                        <h1 class="empty-err-mess">Không có dữ liệu</h1>
                    </div>
                    <DriverItem
                        v-for="(item, index) in listAssets"
                        :type="'asset'"
                        :key="index"
                        :itemProp="item"
                        :itemIndex="index + 1"
                        @showPopup="showPopup"
                        style="width: 100%"
                    ></DriverItem>
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
import DriverItem from '@/components/Driver/DriverItem.vue';
export default {
    components: {
        DriverItem,
    },
    data() {
        return {
            listAssets: [
                {
                    id: '6a386c58-dd12-44b8',
                    address: 'abc',
                    bankId: '970422',
                    bankNumber: '068866789999',
                    email: 'anhanh2003+3@gmail.com',
                    fullName: 'Bui Tuan Anh',
                    password: '$2b',
                    phoneNumber: '0342973670',
                    qrcodeURL: 'https://storage.googleapis.com/oil-picker-project.appspot.com/qr_codes/qr_code_1690124695580.png',
                    role: 'client'
                },
                {
                    id: '6a386c58-dd12-44b8',
                    address: 'abc',
                    bankId: '970422',
                    bankNumber: '068866789999',
                    email: 'anhanh2003+3@gmail.com',
                    fullName: 'Bui Tuan Anh',
                    password: '$2b',
                    phoneNumber: '0342973670',
                    qrcodeURL: 'https://storage.googleapis.com/oil-picker-project.appspot.com/qr_codes/qr_code_1690124695580.png',
                    role: 'client'
                }
            ],
            meta: [],
            currentPage: 1,
            assetID: {},
            listBills: [],
            selectedBill: '',
            isHaveContent: false,
            isShowPopup: false,
            showNotification: false,
            notiAction: '',
            notiObject: '',
            notiType: '',
            searchValue: '',
            timeoutId: null, // thêm biến timeoutId vào component
            selectedOption: '',
            currentAsset: {},
            options: [
                'Đang hoạt động',
                'Đang tạm nghỉ',
                'Đã nghỉ việc',
            ],
        };
    },
    computed: {
        pageParam() {
            return this.$route.query.page;
        },
        pageSearch() {
            return this.$route.query.search;
        },
        pageStatus() {
            return this.$route.query.status;
        },
        pageBill() {
            return this.$route.query.Bill_id;
        }
    },
    mounted() {
        // this.BillID = this.pageBill;
        // this.searchValue = this.pageSearch;
        // this.selectedOption = this.pageStatus;
        // this.BillID = this.pageBill;
        // if(this.BillID != undefined) {
        //     this.fetchDetailBill();
        // }
        // this.refreshData();
        // this.fetchListBills();
    },
    watch: {
        pageParam: async function () {
            this.refreshData();
        },
        listAssets: {
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
            if (this.searchValue !== '' || this.selectedOption !== '' || this.selectedBill) {
                this.Search();
            } else {
                this.fetchData();
            }
        },
        async downloadFile() {
            try {
                const { selectedOption, searchValue, selectedBill } = this;
                let apiURL = '/assets?pageNumber=1&pageSize=10&isConvert=true'; // đường dẫn tới API download file
                if (selectedOption && selectedOption !== 'Tất cả') {
                    apiURL += `&status=${selectedOption}`;
                }
                if (searchValue) {
                    apiURL += `&searchQuery=${searchValue}`;
                }
                if(selectedBill) {
                    apiURL += `&Bill_id=${selectedBill.BillID}`;
                }
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
                link.setAttribute('download', 'SoTheoDoiTSCD.xlsx');
                document.body.appendChild(link);
                link.click();
                // Xóa đối tượng thẻ a để tránh hiển thị thừa trên trang
                document.body.removeChild(link);
                this.setNotification('Export', 'file', 'thành công');
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
            } catch (error) {
                this.setNotification('Export', 'file', 'thất bại');
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
            }
        },
        async fetchData() {
            try {
                const response = await this.$axios.get(
                    `/assets?pageNumber=${this.currentPage}&pageSize=10`
                );
                this.listAssets = response.data.data;
                this.meta = response.data.meta;
                console.log(this.listAssets);
            } catch (error) {
                console.log(error);
                this.setNotification('Tải', 'dữ liệu', 'thất bại');
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
            }
        },
        async fetchDetail(id) {
            try {
                await this.$axios.get(`/assets/${id}`).then((res) => {
                    this.currentAsset = res['data']['data'];
                    console.log(this.currentAsset);
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
        async Search() {
            this.currentPage = this.pageParam;
            try {
                const { currentPage, selectedOption, searchValue, selectedBill, BillID } = this;
                let url = `/assets?pageNumber=${currentPage}&pageSize=10`;
                if (selectedOption && selectedOption !== 'Tất cả') {
                    url += `&status=${selectedOption}`;
                }
                if (searchValue) {
                    url += `&searchQuery=${searchValue}`;
                }
                if(selectedBill) {
                    url += `&Bill_id=${selectedBill.BillID}`;
                }
                if(selectedBill === '' && BillID) {
                    url += `&Bill_id=${BillID}`;
                }
                const {
                    data: { data, meta },
                } = await this.$axios.get(url);
                this.listAssets = data;
                this.meta = meta;
                console.log(this.listAssets);
                // Lưu trạng thái của selectedOption và searchValue vào URL của trang web
                const query = {};
                if (selectedOption) {
                    query.status = selectedOption;
                }
                if (searchValue) {
                    query.search = searchValue;
                }
                if (selectedBill) {
                    query.Bill_id = selectedBill.BillID;
                }
                if(selectedBill === '') {
                    query.Bill_id = BillID;
                }
                this.$router.push({ path: `/assets?page=${currentPage}`, query });
            } catch (error) {
                console.error(error);
                this.setNotification('Tải', 'dữ liệu', 'thất bại');
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
        async addAsset(asset) {
            try {
                await this.$axios.post(`/assets`, {
                    deviceID: asset.deviceID,
                    BillID: asset.BillID,
                    assetName: asset.assetName,
                    yearOfUse: 2023,
                    technicalSpecification: asset.technicalSpecification,
                    quantity: asset.quantity,
                    cost: asset.cost,
                    status: 'Hoạt động tốt',
                    notes: asset.notes,
                });
                this.setNotification('Thêm mới', 'tài sản', 'thành công');
                this.showNotification = true;
                this.refreshData();
                setTimeout(() => {
                    this.showNotification = '';
                }, 3000);
            } catch (error) {
                this.setNotification('Thêm mới', 'tài sản', 'thất bại');
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
                console.log(error);
            }
        },
        async updateAsset(asset) {
            try {
                await this.$axios.put(`/assets/${asset.assetID}`, {
                    assetID: asset.assetID,
                    deviceID: asset.deviceID,
                    BillID: asset.BillID,
                    assetName: asset.assetName,
                    yearOfUse: 2023,
                    technicalSpecification: asset.technicalSpecification,
                    quantity: asset.quantity,
                    cost: asset.cost,
                    status: asset.status,
                    notes: asset.notes,
                });
                this.setNotification('Cập nhật', 'tài sản', 'thành công');
                this.showNotification = true;
                this.refreshData();
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
            } catch (error) {
                this.setNotification('Cập nhật', 'tài sản', 'thất bại');
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
                console.log(error);
            }
        },
        async deleteAsset() {
            try {
                await this.$axios.delete(`/assets/${this.assetID}`);
                this.setNotification('Xóa', 'tài sản', 'thành công');
                this.showNotification = true;
                this.refreshData();
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
            } catch (error) {
                this.setNotification('Xóa', 'tài sản', 'thất bại');
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
                console.log(error);
            }
        },
        async disposeAsset() {
            try {
                await this.$axios.post(`/assets/${this.assetID}`);
                this.setNotification('Thanh lý', 'tài sản', 'thành công');
                this.showNotification = true;
                this.refreshData();
                setTimeout(() => {
                    this.showNotification = '';
                }, 3000);
            } catch (error) {
                this.setNotification('Thanh lý', 'tài sản', 'thất bại');
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
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
        async fetchListBills() {
            try {
                const response = await this.$axios.get(`/Bills`);
                this.listBills = response.data.data;
            } catch (error) {
                console.log(error);
            }
        },
        async fetchDetailBill() {
            try {
                await this.$axios
                    .get(`/Bills/${this.BillID}`)
                    .then((res) => {
                        this.selectedBill = res['data']['data'];
                        console.log(this.selectedBill);
                    });
            } catch (error) {
                console.log(error);
            }
        },
        setNotification(action, object, type) {
            this.notiAction = action;
            this.notiObject = object;
            this.notiType = type;
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
                this.assetID = id;
                this.notiObject = object;
            }
            this.notiAction = action;
        },
        submitForm(action, asset) {
            console.log(action);
            console.log(asset);
            this.isShowPopup = false;
            if (action === 'xóa') {
                this.deleteAsset();
            } else if (action === 'thanh lý') {
                this.disposeAsset();
            } else if (action === 'thêm mới') {
                if (!asset.assetID) {
                    this.addAsset(asset);
                } else {
                    this.updateAsset(asset);
                }
            } else {
                this.downloadFile();
            }
        },
        closePopup() {
            this.isShowPopup = '';
            this.currentAsset = {};
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

</style>