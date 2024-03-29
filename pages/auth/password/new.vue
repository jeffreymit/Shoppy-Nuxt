<template>
    <div class="flex flex-1 flex-col">
        <div class="sm:mx-auto sm:w-full sm:max-w-md">
            <img
                class="mx-auto h-12 w-auto"
                src="~/assets/images/logo.svg"
                width="110"
                alt="Shoppy"
            />
        </div>

        <div class="mt-8 sm:mx-auto sm:w-full sm:max-w-md">
            <div class="bg-white py-4 pb-8 px-4 shadow sm:rounded-lg sm:px-10">
                <h2
                    class="mt-6 text-center text-3xl leading-9 font-extrabold text-gray-900"
                >
                    <template v-if="mode === 'restore'">
                        Restore Password
                    </template>
                    <template v-else>
                        Forgot Password
                    </template>
                </h2>
                <p
                    class="mt-2 text-center text-sm leading-5 text-gray-600 max-w"
                >
                    Or go back to
                    <styled-link to="/auth/login">sign in</styled-link>
                </p>

                <validation-observer
                    v-slot="vo"
                    tag="form"
                    class="mt-8"
                    @submit.prevent
                >
                    <control
                        v-model="user.email"
                        class="mb-6"
                        name="email"
                        type="email"
                        rules="required|email"
                    >
                        <template v-slot:title="{ validation }">
                            <div class="flex flex-col mb-2">
                                <label
                                    class="text-sm font-medium leading-5 text-gray-700 mb-0"
                                    >Email</label
                                >
                                <small class="text-red-400">{{
                                    validation.errors[0]
                                }}</small>
                            </div>
                        </template>
                    </control>

                    <template v-if="mode === 'restore'">
                        <control
                            v-model="user.token"
                            class="mb-6 select-none"
                            name="token"
                            type="text"
                            rules="required|min:8"
                            readonly
                            disabled
                        >
                            <template v-slot:title="{ validation }">
                                <div class="flex flex-col mb-2">
                                    <label
                                        class="text-sm font-medium leading-5 text-gray-700 mb-0"
                                        >Token</label
                                    >
                                    <small class="text-red-400">{{
                                        validation.errors[0]
                                    }}</small>
                                </div>
                            </template>
                        </control>

                        <control
                            v-model="user.password"
                            name="password"
                            type="password"
                            rules="required|min:7"
                        >
                            <template v-slot:title="{ validation }">
                                <div class="flex flex-col mb-2">
                                    <label
                                        class="text-sm font-medium leading-5 text-gray-700 mb-0"
                                        >Password</label
                                    >
                                    <small class="text-red-400">{{
                                        validation.errors[0]
                                    }}</small>
                                </div>
                            </template>
                            <template v-slot:footer>
                                <password-strength
                                    v-model="user.password"
                                    :strength-meter-only="true"
                                />
                            </template>
                        </control>

                        <control
                            v-model="user.password_confirmation"
                            class="mb-6"
                            name="repeat password"
                            type="password"
                            rules="required|confirmed:password"
                        >
                            <template v-slot:title="{ validation }">
                                <div class="flex flex-col mb-2">
                                    <label
                                        class="text-sm font-medium leading-5 text-gray-700 mb-0"
                                        >Repeat Password</label
                                    >
                                    <small class="text-red-400">{{
                                        validation.errors[0]
                                    }}</small>
                                </div>
                            </template>
                        </control>
                    </template>

                    <recaptcha />

                    <div class="mt-6">
                        <span class="block w-full rounded-md shadow-sm">
                            <styled-button
                                type="submit"
                                :disabled="!vo.valid"
                                class="w-full"
                                @click="reset"
                            >
                                <template v-if="mode === 'restore'">
                                    Reset Password
                                </template>
                                <template v-else>
                                    Send Recovery Email
                                </template>
                            </styled-button>
                        </span>
                    </div>
                </validation-observer>
            </div>
        </div>
    </div>
</template>

<script>
import Cookies from 'js-cookie'
import PasswordStrength from 'vue-password-strength-meter'

import Control from '~/components/Control.vue'

export default {
    layout: 'authentication',

    components: {
        Control,
        PasswordStrength
    },

    data() {
        return {
            mode: 'create',
            user: {
                email: '',
                token: '',
                password: '',
                password_confirmation: ''
            }
        }
    },

    head() {
        return {
            title: 'Reset Password'
        }
    },

    mounted() {
        if (this.$route.query.token) {
            this.mode = 'restore'
            this.user.token = this.$route.query.token
        }
    },

    methods: {
        async reset() {
            const data = Object.assign({}, this.user)
            const token = await this.$recaptcha.getResponse()
            data.g_recaptcha_response = token

            const response = await this.$axios.$post(
                this.mode === 'restore'
                    ? '/auth/password/reset'
                    : '/auth/password/new',
                data
            )

            if (response.status) {
                this.$toast.success(response.message)
                if (this.mode === 'restore') {
                    this.$router.push('/auth/login')
                    return
                }
            } else {
                this.$toast.error(response.message)
            }
            this.$recaptcha.reset()
        }
    }
}
</script>
