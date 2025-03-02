<template>
    <div class="page row">
        <div class="col-md-10">
            <InputSearch v-model="searchText" />
        </div>
        <div class="mt-3 col-md-6">
            <h4>
                Danh bạ
                <i class="fas fa-address-book"></i>
            </h4>
            <ContactList v-if="filteredContacts.length > 0" :contacts="filteredContacts" v-model:activeIndex="activeIndex" />
            <p v-else>Không có liên hệ nào.</p>

            <div class="mt-3 row justify-content-around align-items-center">
                <button class="btn btn-sm btn-primary" @click="refreshList">
                    <i class="fas fa-redo"></i> Làm mới
                </button>

                <button class="btn btn-sm btn-success" @click="goToAddContact">
                    <i class="fas fa-plus"></i> Thêm mới
                </button>

                <button class="btn btn-sm btn-danger" @click="removeAllContacts">
                    <i class="fas fa-trash"></i> Xóa tất cả
                </button>
            </div>
        </div>

        <div class="mt-3 col-md-6">
            <div v-if="activeContact">
                <h4>
                    Chi tiết Liên hệ
                    <i class="fas fa-address-card"></i>
                </h4>
                <ContactCard :contact="activeContact" />
                <router-link :to="{ name: 'contact.edit', params: { id: activeContact._id } }">
                    <span class="mt-2 badge badge-warning">
                        <i class="fas fa-edit"></i> Hiệu chỉnh</span>
                </router-link>
            </div>
        </div>
    </div>
</template>

<script>
import ContactCard from '@/components/ContactCard.vue';
import InputSearch from '@/components/InputSearch.vue';
import ContactList from '@/components/ContactList.vue';
import contactService from '@/services/contact.service';

export default {
    components: {
        ContactCard,
        InputSearch,
        ContactList,
    },
    data() {
        return {
            contacts: [],
            activeIndex: -1,
            searchText: "",
        };
    },
    watch: {
        searchText() {
            this.activeIndex = -1;
        },
    },
    computed: {
        filteredContacts() {
            if (!this.searchText) return this.contacts;
            return this.contacts.filter(contact =>
                [contact.name, contact.email, contact.address, contact.phone].join(" ").includes(this.searchText)
            );
        },
        activeContact() {
            return this.activeIndex >= 0 ? this.filteredContacts[this.activeIndex] : null;
        }
    },
    methods: {
        async retrieveContacts() {
            try {
                console.log("Đang gọi API lấy danh bạ...");
                this.contacts = await contactService.getAll();
                console.log("Danh bạ nhận được:", this.contacts);
            } catch (error) {
                console.error("Lỗi khi lấy danh bạ:", error);
            }
        },
        refreshList() {
            console.log("Nút 'Làm mới' được bấm!");
            this.retrieveContacts();
            this.activeIndex = -1;
        },
        async removeAllContacts() {
            console.log("Nút 'Xóa tất cả' được bấm!");
            if (confirm("Bạn muốn xóa tất cả Liên hệ?")) {
                try {
                    console.log("Đang xóa danh bạ...");
                    await contactService.deleteAll();
                    console.log("Xóa thành công!");
                    this.refreshList();
                } catch (error) {
                    console.error("Lỗi khi xóa danh bạ:", error);
                }
            }
        },
        goToAddContact() {
            console.log("Nút 'Thêm mới' được bấm!");
            this.$router.push({ name: 'contact.add' });
        },
        testClick() {
            console.log("Nút kiểm tra hoạt động!");
        }
    },
    mounted() {
        this.refreshList();
    },
};
</script>

<style scoped>
.page {
    text-align: left;
    max-width: 750px;
}
</style>
