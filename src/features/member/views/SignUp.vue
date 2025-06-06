<template>
    <div class="signup-container">
        <h2 class="signup-title">회원가입</h2>
        <form class="signup-form" @submit.prevent="handleSubmit">
            <div class="form-group">
                <label for="name">이름</label>
                <input id="name" type="text" v-model="name" placeholder="이름을 입력해주세요." />
            </div>

            <div class="form-group">
                <label for="memberId">아이디</label>
                <div class="input-with-btn">
                    <input
                            id="memberId"
                            type="text"
                            v-model="memberId"
                            placeholder="6~15자, 영문과 숫자만"
                    />
                    <button type="button" class="check-btn" @click="onCheckMemberId">중복확인</button>
                </div>
            </div>

            <div class="form-group">
                <label for="password">비밀번호</label>
                <input id="password" type="password" v-model="password" placeholder="8자 이상" />
            </div>

            <div class="form-group">
                <input
                        id="passwordCheck"
                        type="password"
                        v-model="passwordCheck"
                        placeholder="비밀번호 재입력"
                />
                <p v-if="passwordCheck && password !== passwordCheck" class="error-message">
                    비밀번호가 일치하지 않습니다.
                </p>
            </div>

            <div class="form-group">
                <label for="nickname">닉네임</label>
                <div class="input-with-btn">
                    <input id="nickname" type="text" v-model="nickname" placeholder="2~10자" />
                    <button type="button" class="check-btn" @click="onCheckNickname">중복확인</button>
                </div>
            </div>

            <div class="form-group">
                <label for="email">E-mail</label>
                <input id="email" type="email" v-model="email" placeholder="예) bookjeok@bookjeok.com" />
            </div>

            <div class="form-group">
                <label for="phone">전화번호</label>
                <input
                        id="phone"
                        type="tel"
                        v-model="phone"
                        placeholder="‘-’ 제외"
                        @input="onPhoneInput"
                />
            </div>

            <div class="form-group">
                <label for="birth">생년월일</label>
                <input
                        id="birth"
                        type="text"
                        v-model="birth"
                        placeholder="생년월일 입력 (예:970816)"
                        @blur="onBirthBlur"
                />
            </div>

            <div class="form-group toggle-group">
                <label for="marketing">마케팅 정보 수신 여부</label>
                <label class="toggle-switch">
                    <input id="marketing" type="checkbox" v-model="marketing" />
                    <span class="slider"></span>
                </label>
            </div>

            <button type="submit" class="signup-btn">회원가입</button>
        </form>

        <Modal :visible="modalVisible" :message="modalMessage" @close="handleModalClose" />
    </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { useAuthStore } from '@/stores/auth'
import Modal from '@/components/common/Modal2.vue'

const router = useRouter()
const authStore = useAuthStore()

const name = ref('')
const memberId = ref('')
const password = ref('')
const passwordCheck = ref('')
const nickname = ref('')
const email = ref('')
const phone = ref('')
const birth = ref('')
const marketing = ref(false)

const modalVisible = ref(false)
const modalMessage = ref('')
const isSignupComplete = ref(false)

function onPhoneInput(e) {
    const onlyNumbers = e.target.value.replace(/[^0-9]/g, '')
    if (e.target.value !== onlyNumbers) {
        modalMessage.value = '올바른 양식으로 작성해주세요!'
        modalVisible.value = true
    }
    phone.value = onlyNumbers
}

function onBirthBlur(e) {
    if (birth.value.length !== 6) {
        modalMessage.value = '올바른 양식으로 작성해주세요!'
        modalVisible.value = true
    }
}

function onCheckMemberId() {
    const regex = /^[a-zA-Z0-9]{6,15}$/
    if (!memberId.value || !regex.test(memberId.value)) {
        modalMessage.value = '올바르지 않은 아이디 형식입니다!'
        modalVisible.value = true
        return
    }
    modalMessage.value = '사용가능한 아이디입니다!'
    modalVisible.value = true
}

function onCheckNickname() {
    if (!nickname.value || nickname.value.length < 2 || nickname.value.length > 10) {
        modalMessage.value = '닉네임은 2자리 이상 10자리 이하여야 합니다!'
        modalVisible.value = true
        return
    }
    modalMessage.value = '사용가능한 닉네임입니다!'
    modalVisible.value = true
}

function handleModalClose() {
    modalVisible.value = false
    if (isSignupComplete.value) {
        router.push('/login')
        isSignupComplete.value = false
    }
}

async function handleSubmit() {
    if (
            !name.value.trim() ||
            !memberId.value.trim() ||
            !password.value.trim() ||
            !passwordCheck.value.trim() ||
            !nickname.value.trim() ||
            !email.value.trim() ||
            !phone.value.trim() ||
            !birth.value.trim()
    ) {
        modalMessage.value = '모든 항목을 입력해주세요!'
        modalVisible.value = true
        return
    }

    if (password.value.length < 8) {
        modalMessage.value = '비밀번호는 8자 이상이어야 합니다!'
        modalVisible.value = true
        return
    }

    const regex = /^[a-zA-Z0-9]{6,15}$/
    if (!regex.test(memberId.value)) {
        modalMessage.value = '올바르지 않은 아이디 형식입니다!'
        modalVisible.value = true
        return
    }

    if (nickname.value.length < 2 || nickname.value.length > 10) {
        modalMessage.value = '닉네임은 2자리 이상 10자리 이하여야 합니다!'
        modalVisible.value = true
        return
    }

    if (password.value !== passwordCheck.value) {
        modalMessage.value = '비밀번호가 일치하지 않습니다.'
        modalVisible.value = true
        return
    }

    const success = await authStore.signup({
        name: name.value,
        memberId: memberId.value,
        password: password.value,
        nickname: nickname.value,
        email: email.value,
        phone: phone.value,
        birth: birth.value,
        marketing: marketing.value,
    })

    if (success) {
        modalMessage.value = '회원가입이 완료되었습니다!'
        isSignupComplete.value = true
    } else {
        modalMessage.value = '회원가입에 실패했습니다. 다시 시도해주세요.'
    }

    modalVisible.value = true
}
</script>

<style scoped>

.signup-container {
    max-width: 430px;
    margin: 56px auto 0 auto;
    padding: 0 0 48px 0;
    background: #fff;
}
.signup-title {
    text-align: center;
    font-size: 2rem;
    font-weight: 700;
    color: #391902;
    margin-bottom: 38px;
    letter-spacing: -1px;
}
.signup-form {
    display: flex;
    flex-direction: column;
    gap: 0;
}
.form-group {
    display: flex;
    flex-direction: column;
    margin-bottom: 18px;
    position: relative;
}
.form-group label {
    font-size: 15px;
    font-weight: 500;
    color: #391902;
    margin-bottom: 7px;
}
.input-with-btn {
    position: relative;
    width: 100%;
    display: flex;
    align-items: center;
}
.input-with-btn input {
    width: 100%;
    height: 38px;
    padding: 0 70px 0 14px;
    border: 1px solid #e5e5e5;
    border-radius: 6px;
    font-size: 15px;
    background: #fff;
    box-sizing: border-box;
    outline: none;
    transition: border 0.2s;
}
.input-with-btn input:focus {
    border-color: #d1bfa3;
}
.input-with-btn .check-btn {
    position: absolute;
    right: 8px;
    top: 50%;
    transform: translateY(-50%);
    height: 28px;
    padding: 0 10px;
    background: #f9f0df;
    color: #391902;
    border: 1px solid #e5e5e5;
    border-radius: 5px;
    font-size: 13px;
    font-weight: 500;
    cursor: pointer;
    transition: background 0.2s;
    line-height: 28px;
    white-space: nowrap;
    box-shadow: none;
    min-width: 52px;
    text-align: center;
}
.input-with-btn .check-btn:hover {
    background: #f2e1c4;
}
input[type="text"],
input[type="password"],
input[type="email"],
input[type="tel"] {
    width: 100%;
    height: 38px;
    padding: 0 14px;
    border: 1px solid #e5e5e5;
    border-radius: 6px;
    font-size: 15px;
    background: #fff;
    box-sizing: border-box;
    outline: none;
    transition: border 0.2s;
    font-weight: 400;
}
input:focus {
    border-color: #d1bfa3;
}
.error-message {
    color: #e74c3c;
    font-size: 12px;
    margin: 4px 0 0 2px;
    line-height: 1.4;
}
.toggle-group {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 32px;
    padding-right: 0;
}
.toggle-group label:first-child {
    margin-right: 0;
    min-width: unset;
    font-size: 15px;
    color: #391902;
}
.toggle-switch {
    position: relative;
    display: inline-block;
    width: 38px;
    height: 22px;
    vertical-align: middle;
    margin-left: auto;
}
.toggle-switch input {
    opacity: 0;
    width: 0;
    height: 0;
}
.slider {
    position: absolute;
    cursor: pointer;
    top: 0; left: 0; right: 0; bottom: 0;
    background-color: #e5e5e5;
    border-radius: 22px;
    transition: background 0.2s;
}
.slider:before {
    position: absolute;
    content: "";
    height: 16px;
    width: 16px;
    left: 3px;
    bottom: 3px;
    background-color: #fff;
    border-radius: 50%;
    transition: transform 0.2s;
    box-shadow: 0 1px 4px rgba(0,0,0,0.08);
}
.toggle-switch input:checked + .slider {
    background-color: #f9f0df;
}
.toggle-switch input:checked + .slider:before {
    transform: translateX(16px);
}
.signup-btn {
    width: 100%;
    height: 38px;
    background: #f9f0df;
    color: #391902;
    font-size: 16px;
    font-weight: 600;
    border: none;
    border-radius: 6px;
    cursor: pointer;
    margin-top: 16px;
    transition: background 0.2s;
    box-shadow: none;
    letter-spacing: 0.5px;
}
.signup-btn:hover {
    background: #f2e1c4;
}
</style>
