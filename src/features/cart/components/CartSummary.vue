<script setup>
const props = defineProps({
    items: Array,
    totalPrice: Number
});

const emit = defineEmits(['order-now']);
</script>

<template>
    <div id="order-detail-info" class="border fw-bold rounded-4 border-black shadow-sm">
        <div class="mb-3 d-flex justify-content-between">
            <span>상품 금액</span>
            <strong>{{ totalPrice.toLocaleString() }}원</strong>
        </div>
        <div class="mb-3 d-flex justify-content-between">
            <span>배송비</span>
            <span>0원</span>
        </div>
        <div class="mb-3 d-flex justify-content-between">
            <span>상품 할인</span>
            <span>0원</span>
        </div>
        <hr/>
        <div class="mb-3 d-flex justify-content-between fw-bold">
            <span>결제 예정 금액</span>
            <span>{{ totalPrice.toLocaleString() }}원</span>
        </div>
        <div class="mb-3 d-flex justify-content-between">
            <span>적립 예정 포인트</span>
            <span>0P</span>
        </div>
        <button id="order-button" class="btn w-100 fw-bold" @click="emit('order-now')">주문하기</button>
    </div>
</template>

<style scoped>
#order-detail-info {
    width: 270px;
    height: 325px;
    padding: 1.5rem;
    margin-left: 2rem;

    position: sticky;
    top: 135px;
    align-self: flex-start;
}

#order-button {
    background-color: #f9f0df;
    box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.25);
    margin-top: 18px;
}
</style>