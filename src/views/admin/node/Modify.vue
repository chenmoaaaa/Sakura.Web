<template>
    <div id="wapper">
        <br>
        <el-card>
            <div slot="header" class="clearfix">
                <span>{{ $t('admin.node.create.title') }}</span>
            </div>
            <el-form label-width="80px" :rules="rules" status-icon ref="form" :model="form">
                <el-form-item :label="$t('admin.node.create.form.name')" prop="name">
                    <el-input
                        :placeholder="$t('admin.node.create.form.placeholder-name')"
                        v-model="form.name">
                    </el-input>
                </el-form-item>

                <el-form-item :label="$t('admin.node.create.form.address')" prop="address">
                    <el-input
                        :placeholder="$t('admin.node.create.form.placeholder-address')"
                        v-model="form.address">
                    </el-input>
                </el-form-item>

                <el-form-item :label="$t('admin.node.create.form.type')">
                    <el-radio-group v-model="form.kind">
                        <el-radio-button
                            v-for="(thisType, index) in nodeType"
                            :key="index"
                            :label="thisType">
                            {{ nodeKindToType(thisType) }}
                        </el-radio-button>
                    </el-radio-group>
                </el-form-item>

                <el-form-item :label="$t('admin.node.create.form.enable')">
                    <el-switch v-model="form.enable"></el-switch>
                </el-form-item>

                <el-form-item :label="$t('admin.node.create.form.rate')" prop="rate">
                    <el-input
                        :placeholder="$t('admin.node.create.form.placeholder-rate')"
                        v-model.number="form.rate">
                    </el-input>
                </el-form-item>

                <el-form-item :label="$t('admin.node.create.form.level')" prop="level">
                    <el-input
                        :placeholder="$t('admin.node.create.form.placeholder-level')"
                        v-model.number="form.level">
                    </el-input>
                </el-form-item>


                <transition name="fade">
                    <el-form-item :label="$t('admin.node.create.form.port')" v-if="form.kind === 1" prop="port">
                        <el-input
                            :placeholder="$t('admin.node.create.form.placeholder-port')"
                            v-model.number="form.port">
                        </el-input>
                    </el-form-item>
                </transition>

                <el-form-item :label="$t('admin.node.create.form.detail')" prop="detail">
                    <el-input type="textarea" v-model="form.detail"></el-input>
                </el-form-item>

                <el-form-item>
                    <el-button @click="onHandOut" type="primary">
                        {{ $t('admin.node.create.submit') }}
                    </el-button>
                    <el-button @click="$router.push({ name: 'AdminNodeLIst' })">
                        {{ $t('static.cancel') }}
                    </el-button>
                </el-form-item>
            </el-form>
        </el-card>
    </div>
</template>

<script>
    import { getNodeTypes, nodeKindToType } from '@/utils/node';
    import { create } from '@/api/admin/node';

    export default {
        data() {
            return {
                nodeType: getNodeTypes(),
                type: 'create',
                loading: false,
                form: {
                    name: '',
                    address: '',
                    enable: true,
                    kind: 0,
                    rate: 1,
                    level: 0,
                    port: 443,
                    detail: '',
                },
                rules: {
                    name: [
                        { required: true, message: this.$t('admin.node.create.form.error.non-name') },
                    ],
                    address: [
                        { required: true, message: this.$t('admin.node.create.form.error.non-address') },
                    ],
                    level: [
                        { required: true, message: this.$t('admin.node.create.form.error.non-level') },
                        { type: 'number', message: this.$t('admin.node.create.form.error.number-level') },
                    ],
                    rate: [
                        { required: true, message: this.$t('admin.node.create.form.error.non-rate') },
                        { type: 'number', message: this.$t('admin.node.create.form.error.number-rate') },
                    ],
                    port: [
                        { required: true, message: this.$t('admin.node.create.form.error.non-port') },
                        { type: 'number', message: this.$t('admin.node.create.form.error.number-port') },
                    ],
                },
            };
        },
        methods: {
            nodeKindToType,
            async onHandOut() {
                // Fixme: Debug
                const status = await this.$refs.form.validate();
                if (status) {
                    this.loading = true;
                    try {
                        await create(this.form);
                        this.$notify.info({
                            title: this.$t('admin.node.create.message-title'),
                            message: this.$t('admin.node.create.success'),
                        });
                        setTimeout(() => {
                            this.$router.push({ name: 'AdminNodeList' });
                        }, 2000);
                    } catch (err) {
                        this.$notify.error({
                            title: this.$t('admin.node.create.message-title'),
                            message: err.response && err.response.data.error
                                ? err.response.data.error
                                : err.message,
                        });
                    } finally {
                        this.loading = false;
                    }
                }
            },
        },
    };
</script>


<style scoped>
    .fade-enter-active, .fade-leave-active {
        transition: opacity .5s
    }

    .fade-enter, .fade-leave-to /* .fade-leave-active in below version 2.1.8 */ {
        opacity: 0
    }
</style>