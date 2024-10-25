<template>
    <div v-if="show" class="relative z-10 rounded-xl" role="dialog" aria-modal="true">
        <div class="fixed inset-0 hidden bg-gray-500 bg-opacity-75 transition-opacity md:block"></div>
        <div class="fixed inset-0 z-10 w-screen overflow-y-auto rounded-xl">
            <div
                class="flex min-h-full items-stretch justify-center flex-col text-center md:items-center md:px-2 lg:px-4">
                <div
                    class="flex  w-full transform text-left text-base transition md:my-8 md:max-w-2xl md:px-4 lg:max-w-4xl">
                    <div
                        class="relative flex w-full items-center overflow-hidden bg-white rounded-2xl px-4 pb-8 pt-14 shadow-2xl sm:px-6 sm:pt-8 md:p-6 lg:p-8">

                        <button @click="closeModal" type="button"
                            class="absolute right-4 top-4 text-gray-400 hover:text-gray-500 sm:right-6 sm:top-8 md:right-6 md:top-6 lg:right-8 lg:top-8">
                            <span class="sr-only">Close</span>
                            <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                                stroke="currentColor" aria-hidden="true">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
                            </svg>
                        </button>
                        <div
                            class="grid w-full grid-cols-1 items-start gap-x-6 gap-y-8 sm:grid-cols-12 lg:gap-x-8">
                            <div class="sm:col-span-4 lg:col-span-5">
                                <div class="aspect-h-1 aspect-w-1 overflow-hidden rounded-lg bg-gray-100">
                                    <img :src="product.imgUrl"
                                        alt="Back angled view with bag open and handles to the side."
                                        class="object-cover object-center">
                                </div>
                            </div>
                            <div class="sm:col-span-8 lg:col-span-7">
                                <!-- <h2 class="text-2xl font-bold text-gray-900 sm:pr-12">{{ product.name }}</h2> -->

                                <section aria-labelledby="information-heading" class="mt-3">
                                    <h3 id="information-heading" class="sr-only">Product information</h3>

                                    <p class="text-2xl text-gray-900">₦ {{ product.price }}</p>
                                    <p class="mt-2">
                                        <span class="text-xs rounded-full bg-green-500 px-6 py-1.5 text-white">{{
        product.stock }} units available in stock</span>
                                    </p>
                                    <div class="mt-3">
                                        <h4 class="sr-only">Reviews</h4>
                                        <div class="flex items-center">
                                            <div class="flex items-center">
                                                <ProductStarRating :rating="Number(product.rating)" />
                                            </div>
                                            <p class="sr-only">3.9 out of 5 stars</p>
                                        </div>
                                    </div>

                                    <div class="mt-6">
                                        <h4 class="sr-only">Description</h4>

                                        <p class="text-sm text-gray-700">{{ product.description }}</p>
                                    </div>
                                </section>

                                <section aria-labelledby="options-heading" class="mt-6">
                                    <h3 id="options-heading" class="sr-only">Product options</h3>

                                    <div class="space-y-4">
                                        <div>
                                            <h4 class="text-sm text-gray-600">Size</h4>
                                            <ProductSizes @selectedSize="handleSelectedSize"
                                                :sizeList="product.sizeList" />
                                        </div>
                                        <div class="space-y-3">
                                            <!-- <h4 class="text-sm text-gray-600">Quantity</h4> -->

                                            <ProductQuantity @counter="handleCount" />
                                        </div>
                                        <div class="">
                                            <button class="font-semibold text-gray-900" type="button"
                                                @click="toggleTestimonials">{{ showTestimonials ? 'Hide Testimonials' :
        'Show Testimonials' }}</button>
                                        </div>

                                        <div v-if="showTestimonials" id="tab-panel-reviews" class=""
                                            aria-labelledby="tab-reviews" role="tabpanel" tabindex="0">
                                            <h3 class="sr-only">Customer Reviews</h3>

                                            <ProductCustomerTestimonials v-for="(itm, idx) in product.testimonials"
                                                :key="idx" :review="itm" />
                                        </div>

                                        <div class="mt-6 flex items-center gap-x-4">
                                            <button @click="handleCart" type="button"
                                                class="flex w-full items-center justify-center rounded-md border border-transparent bg-indigo-600 px-8 py-3 text-base font-medium text-white hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 focus:ring-offset-gray-50">Add
                                                to cart</button>
                                            <button type="button" @click="handlePayNow(product)"
                                                class="flex w-full items-center justify-center rounded-md border border-transparent bg-indigo-600 px-8 py-3 text-base font-medium text-white hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 focus:ring-offset-gray-50">Buy
                                                now</button>
                                        </div>
                                    </div>
                                </section>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>


</template>

<script setup lang="ts">
import { useFlutterwaveSDK } from '@/composables/payment/flutterwave'
import { useCashPayment } from '@/composables/payment/cashPayment'
import { usePopulateCart } from '@/composables/cart/usePopulateCart'
const { handleCashPayment, processing: processingCashPayment } = useCashPayment()
const { populateCart } = usePopulateCart()
const { handlePayment, paymentForm, loading } = useFlutterwaveSDK()
const router = useRouter()
const props = defineProps<{ show: boolean, product: any }>();
const emit = defineEmits(['update:show']);
const showTestimonials = ref(false)
const closeModal = () => {
    emit('update:show', false);
};

const handlePayNow = async (data: any) => {
    handlePayment(data)
//   await triggerPayment();
};
const productCount = ref(1)
const productSize = ref('S')

const toggleTestimonials = () => {
    showTestimonials.value = !showTestimonials.value
}

const handleCart = (payload: any) => {
    const { sizeList, testimonials, ...rest } = props.product;
    populateCart({
        productCount: productCount.value,
        ...rest,
        productSize: productSize.value,
        paymentMode: payload.type
    })
    closeModal()
}


const handleCount = (count: any) => {
    productCount.value = count
}

const handleSelectedSize = (size: any) => {
    productSize.value = size
}
</script>

<style scoped>
@media (min-width: 768px) {
    .modal-content {
        width: 50%;
    }
}

.form-radio {
    --tw-ring-inset: var(--tw-empty,
            /*!*/
            /*!*/
        );
    --tw-ring-offset-width: 0px;
    --tw-ring-offset-color: #fff;
    --tw-ring-color: rgba(59, 130, 246, 0.5);
    --tw-ring-opacity: 1;
    --tw-ring-shadow: 0 0 #0000;
    --tw-shadow: 0 0 #0000;
}
</style>
