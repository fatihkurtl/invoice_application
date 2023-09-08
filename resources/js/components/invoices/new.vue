<script setup>
import axios from 'axios';
import { ref, onMounted } from 'vue';
import router from '../../router';

const form = ref([]);
const allCustomers = ref([]);
const customer_id = ref([]);
const item = ref([]);
const listCard = ref([]);
const listProducts = ref([]);

const showModal = ref(false);
const hideModal = ref(true);

onMounted(async () => {
    indexForm();
    getAllCustomers();
    getProducts();
});

const indexForm = async () => {
    let response = await axios.get('/api/create_invoice')
    // console.log('form', response.data);
    form.value = response.data;
}

const getAllCustomers = async () => {
    let response = await axios.get('/api/customers')
    // console.log('response :>> ', response);
    allCustomers.value = response.data.customers;
}

const addCard = async (item) => {

    const itemCard = {
        id: item.id,
        item_code: item.item_code,
        description: item.description,
        unit_price: item.unit_price,
        quantity: item.quantity,
    };
    listCard.value.push(itemCard);
    closeModal();
}

const openModal = async () => {
    showModal.value = !showModal.value;
}

const closeModal = async () => {
    showModal.value = !hideModal.value;
}

const getProducts = async () => {
    let response = await axios.get('/api/products');
    console.log('products :>> ', response);
    listProducts.value = response.data.products;
}

const removeItem = async (i) => {
    listCard.value.splice(i, 1);
}

const SubTotal = () => {
    let total = 0;
    listCard.value.map((data)=> {
        total = total + (data.quantity * data.unit_price)
    });
    return total;
}

const Total = () => {
    return SubTotal() - form.value.discount;
}

const onSave = () => {
    if(listCard.value.length >= 1) {
        let subtotal = 0;
        subtotal = SubTotal();

        console.log('id', customer_id.value);

        let total = 0;
        total = Total();

        const formData = new FormData();
        formData.append('invoice_item', JSON.stringify(listCard.value));
        formData.append('customer_id', customer_id.value);
        // formData.append('customer_id', Math.floor(Math.random() * 100) + 1);
        formData.append('date', form.value.date);
        formData.append('due_date', form.value.due_date);
        formData.append('number', form.value.number);
        formData.append('reference', form.value.reference);
        formData.append('discount', form.value.discount);
        formData.append('sub_total', subtotal);
        formData.append('total', total);
        formData.append('terms_and_conditions', form.value.terms_and_conditions);

        axios.post('/api/add_invoice', formData);
        listCard.value = [];
        router.push('/');
    }
}

</script>

<template>
    <div class="container">
        <div class="invoices">

            <div class="card__header">
                <div>
                    <h2 class="invoice__title">New Invoice</h2>
                </div>
                <div>

                </div>
            </div>

            <div class="card__content">
                <div class="card__content--header">
                    <div>
                        <p class="my-1">Customer</p>
                        <select v-model="customer_id" name="" id="" class="input">
                            <option disabled>Selected customer</option>
                            <option :value="customer.id" v-for="customer in allCustomers" :key="customer.id">
                                {{ customer.firstname }}
                            </option>
                        </select>
                    </div>
                    <div>
                        <p class="my-1">Date</p>
                        <input v-model="form.date" id="date" placeholder="dd-mm-yyyy" type="date" class="input"> <!---->
                        <p class="my-1">Due Date</p>
                        <input v-model="form.due_date" id="due_date" type="date" class="input">
                    </div>
                    <div>
                        <p class="my-1">Numero</p>
                        <input v-model="form.number" type="text" class="input">
                        <p class="my-1">Reference(Optional)</p>
                        <input v-model="form.reference" type="text" class="input">
                    </div>
                </div>
                <br><br>
                <div class="table">

                    <div class="table--heading2">
                        <p>Item Description</p>
                        <p>Unit Price</p>
                        <p>Qty</p>
                        <p>Total</p>
                        <p></p>
                    </div>

                    <!-- item 1 -->
                    <div v-for="(itemCard, i) in listCard" :key="itemCard.id" class="table--items2">
                        <p>#{{ itemCard.item_code }} {{ itemCard.description }}</p>
                        <p>
                            <input v-model="itemCard.unit_price" type="text" class="input">
                        </p>
                        <p>
                            <input v-model="itemCard.quantity" type="text" class="input">
                        </p>
                        <p v-if="itemCard.quantity">
                            $ {{ (itemCard.quantity) * (itemCard.unit_price) }}
                        </p>

                        <p v-else></p>

                        <p style="color: red; font-size: 24px;cursor: pointer;" @click="removeItem(i)">
                            &times;
                        </p>
                    </div>
                    <div style="padding: 10px 30px !important;">
                        <button @click="openModal()" class="btn btn-sm btn__open--modal">Add New Line</button>
                    </div>
                </div>

                <div class="table__footer">
                    <div class="document-footer">
                        <p>Terms and Conditions</p>
                        <textarea v-model="form.terms_and_conditions" cols="50" rows="7" class="textarea"></textarea>
                    </div>
                    <div>
                        <div class="table__footer--subtotal">
                            <p>Sub Total</p>
                            <span>$ {{ SubTotal() }}</span>
                        </div>
                        <div class="table__footer--discount">
                            <p>Discount</p>
                            <input v-model="form.discount" type="text" class="input">
                        </div>
                        <div class="table__footer--total">
                            <p>Grand Total</p>
                            <span>$ {{ Total() }}</span>
                        </div>
                    </div>
                </div>


            </div>
            <div class="card__header" style="margin-top: 40px;">
                <div>

                </div>
                <div>
                    <a @click="onSave()" class="btn btn-secondary">
                        Save
                    </a>
                </div>
            </div>

        </div>
        <!--==================== add modal items ====================-->
        <div :class="{ show: showModal }" class="modal main__modal ">
            <div class="modal__content">
                <span @click="closeModal()" class="modal__close btn__close--modal">×</span>
                <h3 class="modal__title">Add Item</h3>
                <hr><br>
                <div class="modal__items">
                    <ul style="list-style: none;">
                        <li v-for="(item, i) in listProducts" :key="item.id"
                            style="display: grid;grid-template-columns: 30px 350px 15px;align-items: center;padding-bottom: 5px;">
                            <p>{{ i + 1 }}</p>
                            <a href="#">{{ item.item_code }} {{ item.description }}</a>
                            <button @click="addCard(item)"
                                style="border: 1px solid #e0e0e0;width:35px;height:35px;cursor:pointer;">
                                +
                            </button>
                        </li>
                    </ul>
                </div>
                <br>
                <hr>
                <div class="model__footer">
                    <button @click="closeModal()" class="btn btn-light mr-2 btn__close--modal">
                        Cancel
                    </button>
                    <button class="btn btn-light btn__close--modal ">Save</button>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped></style>
