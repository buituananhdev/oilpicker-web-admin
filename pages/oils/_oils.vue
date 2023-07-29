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
        <CreateOil
            :type="'update'"
            :organizationProp="currentOrganization"
            v-if="isShowPopup === 'thêm mới'"
            @closePopup="closePopup"
            @submitForm="submitForm"
        >
        </CreateOil>
        <Header class="page-top"></Header>
        <TabLeft @closeTab="closeTab()" @openTab="openTab()"></TabLeft>
        <div class="main-content">
            <div class="table-assets container">
                <div class="box">
                    <p>Giá</p>
                    <div class="content">{{moneyFormart}}</div>
                    <div class="symbol">
                        <div>vnd</div>
                        <div>/</div>
                        <div>lit</div>
                    </div>
                </div>
                <div class="box">
                    <p>Dầu trong kho</p>
                    <div class="content">10000</div>
                    <div class="symbol">
                        <div>lit</div>
                    </div>
                </div>
                <div class="box">
                    <p>Dầu đã bán</p>
                    <div class="content">100000</div>
                    <div class="symbol">
                        <div>lit</div>
                    </div>
                </div>
            </div>
            <!-- <div class="page-main"> -->
                <!-- <h1 class="page-main-title">Danh sách dầu</h1> -->
                <!-- <div class="action-container">
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
                    <multiselect
                        class="multiselect"
                        :options="options"
                        v-model="selectedOption"
                        placeholder="Loại tổ chức"
                        @input="Search"
                    ></multiselect>
                    <div class="btn-container">
                        <button
                            class="create-btn"
                            @click="showPopup('xuất file', 'bảng dữ liệu')"
                        >
                            Xuất file excel
                        </button>
                        <button
                            class="create-btn"
                            @click="isShowPopup = 'thêm mới'"
                        >
                            Thêm dầu
                        </button>
                    </div>
                </div> -->
                <!-- <div class="table-assets">
                    <span class="table-assets-title div-center">
                        <p class="div-center stt-col">STT</p>
                        <p class="div-center name-col">
                            Tên dầu
                        </p>
                        <p class="div-center price-col">
                            Giá dầu
                        </p>
                        <p class="div-center available-col">
                            Lượng dầu còn
                        </p>
                        <p class="div-center sold-col">
                            Lượng dầu đã bán
                        </p>
                        <p class="tool-col"></p>
                    </span>
                    <div class="empty-icn div-center" v-show="!isHaveContent">
                        <img
                            src="../../static/icons/file-question.svg"
                            alt="file-rong"
                        />
                        <h1 class="empty-err-mess">Không có dữ liệu</h1>
                    </div>
                    <OilItem
                        v-for="(item, index) in listOil"
                        :type="'organization'"
                        :key="index"
                        :listOil[0].pricem"
                        :itemIndex="index + 1"
                        @showPopup="showPopup"
                        style="width: 100%"
                    ></OilItem>
                </div> -->
                
                <!-- <div class="pagination">
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
            </div> -->
        </div>
    </div>
</template>

<script>
import OilItem from '@/components/Oil/OilItem.vue';
import CreateOil from '@/components/Oil/CreateOil.vue';
export default {
    components: {
        OilItem,
        CreateOil,
    },
    data() {
        return {
            listOil: [
                {
                    price: '20000',
                    available: '200',
                    sold: '1000'
                }
            ],
            meta: [],
            currentPage: 1,
            organizationID: {},
            isHaveContent: false,
            isShowPopup: '',
            showNotification: false,
            notiAction: '',
            notiObject: '',
            notiType: '',
            searchValue: '',
            timeoutId: null, // thêm biến timeoutId vào component
            selectedOption: '',
            currentOrganization: {},
            options: ['Tất cả', 'Khoa', 'Phòng ban', 'Trung tâm'],
            moneyFormart: ''
        };
    },
    computed: {
        pageParam() {
            return this.$route.query.page;
        },
        pageSearch() {
            return this.$route.query.search;
        },
        pageType() {
            return this.$route.query.type;
        },
        
    },
    mounted() {
        // this.searchValue = this.pageSearch;
        // this.selectedOption = this.pageType;
        // if (this.searchValue !== '' || this.selectedOption !== '') {
        //     this.Search();
        // } else {
        //     this.fetchData();
        // }
        console.log("listOil:", this.listOil);
            if (this.listOil.length > 0) {
                console.log("listOil[0]:", this.listOil[0]);
                console.log("price:", this.listOil[0].price);
            } else {
                console.log("listOil is empty or undefined.");
            }
        this.moneyFormart = this.listOil[0].price.toLocaleString('vi-VN', {
            style: 'currency',
            currency: 'VND',
        });
    },
    watch: {
        pageParam: async function () {
            if (this.searchValue !== '' || this.selectedOption !== '') {
                this.Search();
            } else {
                this.fetchData();
            }
        },
        listOil: {
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
        async downloadFile() {
            const { selectedOption, searchValue } = this;
            let apiURL = `/oil?pageNumber=1&pageSize=10&isConvert=true`;
            if (selectedOption && selectedOption !== 'Tất cả') {
                apiURL += `&organizationType=${selectedOption}`;
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
                link.setAttribute('download', 'SoTheoDoiToChuc.xlsx');
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
                    `/oil?pageNumber=${this.currentPage}&pageSize=10`
                );
                this.listOil = response.data.data;
                this.meta = response.data.meta;
                console.log(this.listOil);
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
        async fetchDetail(id) {
            try {
                await this.$axios.get(`/oil/${id}`).then((res) => {
                    this.currentOrganization = res['data']['data'];
                    console.log(this.currentOrganization);
                });
            } catch (error) {
                console.log(error);
            }
        },
        async Search() {
            this.currentPage = this.pageParam;
            try {
                const { currentPage, selectedOption, searchValue } = this;
                let url = `/oil?pageNumber=${currentPage}&pageSize=10`;
                if (selectedOption && selectedOption !== 'Tất cả') {
                    url += `&organizationType=${selectedOption}`;
                }
                if (searchValue) {
                    url += `&searchQuery=${searchValue}`;
                }
                const {
                    data: { data, meta },
                } = await this.$axios.get(url);
                this.listOil = data;
                this.meta = meta;
                console.log(this.listOil);
                // Lưu trạng thái của selectedOption và searchValue vào URL của trang web
                const query = {};
                if (selectedOption) {
                    query.type = selectedOption;
                }
                if (searchValue) {
                    query.search = searchValue;
                }
                this.$router.push({
                    path: `/oil?page=${currentPage}`,
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
        async addOrganization(organization) {
            try {
                await this.$axios.post(`/oil`, {
                    organizationID: '',
                    organizationName: organization.organizationName,
                    organizationType: organization.organizationType,
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
        async updateOrganization(organization) {
            try {
                await this.$axios.put(
                    `/oil/${organization.organizationID}`,
                    {
                        organizationID: organization.organizationID,
                        organizationName: organization.organizationName,
                        organizationType: organization.organizationType,
                    }
                );
                this.fetchData();
                this.notiAction = 'Cập nhật';
                this.notiObject = 'tổ chức';
                this.notiType = 'thành công';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
            } catch (error) {
                this.notiAction = 'Cập nhật';
                this.notiObject = 'tổ chức';
                this.notiType = 'thất bại';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = '';
                }, 3000);
                console.log(error);
            }
        },
        async deleteOrganization() {
            try {
                await this.$axios.delete(
                    `/oil/${this.organizationID}`
                );
                this.notiAction = 'Xóa';
                this.notiObject = 'tổ chức';
                this.notiType = 'thành công';
                this.showNotification = true;
                setTimeout(() => {
                    this.showNotification = false;
                }, 3000);
                this.fetchData();
            } catch (error) {
                this.notiAction = 'Xóa';
                this.notiObject = 'tổ chức';
                this.notiType = 'thất bại';
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
                this.organizationID = id;
                console.log(id);
            }
            this.notiAction = action;
        },
        submitForm(action, organization) {
            console.log(action);
            this.isShowPopup = false;
            if (action === 'xóa') {
                this.deleteOrganization();
            } else if (action === 'thêm mới') {
                if (!organization.organizationID) {
                    this.addOrganization(organization);
                } else {
                    this.updateOrganization(organization);
                }
            } else {
                this.downloadFile();
            }
        },
        closePopup() {
            this.isShowPopup = '';
            this.currentOrganization = {};
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
    width: 10%;
}
.name-col{
    width: 30%;
}
.available-col{
    width: 15%;
}
.sold-col{
    width: 15%;
}
.price-col{
    width: 25%;
}
.tool-col{
    width: 5%;
}
.container{
    display: flex;
    flex-wrap: wrap;
    gap: 50px;
    width: 100%;
    padding: 50px;
    justify-content: space-around;
}
.box{
    border: 2px solid rgb(186, 186, 186);
    padding: 20px;
    width: calc((100% - 50px * 2)/3);
    height: 200px;
    border-radius: 10px;
    position: relative;
}
.box p{
    font-size: 24px;
    font-weight: 700;
    color: #ca7616;
    position: absolute;
}
.box .content{
    display: flex;
    width: 100%;
    height: 100%;
    justify-content: center;
    align-items: center;
    font-size: 44px;
    color: #F8982B;
    font-weight: 800;
}

.box .symbol{
    position: absolute;
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    transform: translate(20%, -75%);
    font-size: 44px;
    color: #F8982B;
    font-weight: 800;
}
</style>
