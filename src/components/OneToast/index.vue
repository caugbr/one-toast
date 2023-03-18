<script>
import Vue from 'vue'
import EmbedStyle from './EmbedStyle.vue'

/**
 * One Toast - Vue 2.x
 * ---------
 * By Cau Guanabara in 12/03/2023
 */

export default {
    name: 'OneToast',
    components: { EmbedStyle },
    props: {
        positionX: {
            type: String,
            default: 'left',
            validator(value) {
                return /^(left|center|right)$/.test(value)
            }
        },
        positionY: {
            type: String,
            default: 'bottom',
            validator(value) {
                return /^(top|center|bottom)$/.test(value)
            }
        },
        useIcon: {
            type: Boolean,
            default: true
        },
        transition: {
            type: String,
            default: 'slide-fade'
        },
        duration: {
            type: Number,
            default: 350
        },
        showCounterBadge: {
            type: Boolean,
            default: false
        },
        progressBar: {
            type: Boolean,
            default: true
        },
        backdrop: {
            type: [Boolean, Object],
            default: false
        },
        closeOnBlur: {
            type: Boolean,
            default: false
        }
    },
    data() {
        return {
            msgs: [],
            curMsg: false,
            visible: false,
            inProgress: false,
            autoCloseHandler: 0
        };
    },
    computed: {
        cssClass() {
            let cls = [
                'one-toast', 
                `${this.positionY}-${this.positionX}`
            ];
            if (this.backdropVisible && this.curMsg) {
                cls.push('block-page');
            }
            return cls.join(' ');
        },
        isVisible: {
            get() {
                return (!!this.curMsg && this.visible);
            },
            set(value) {
                this.visible = !!value;
            }
        },
        cssRules() {
            let rules = [
                {
                    selector: '.one-toast .toast',
                    props: {
                        animationDuration: `${this.duration}ms !important`,
                        transitionDuration: `${this.duration}ms !important`
                    }
                },
                {
                    selector: ':root',
                    props: {
                        '--backdrop-color': this.backdropProps.color,
                        '--backdrop-opacity': this.backdropProps.opacity
                    }
                }
            ];
            return rules;
        },
        iconVisible() {
            return (this.useIcon !== false);
        },
        toastClass() {
            let cn = 'toast';
            if (this.curMsg.customClass) {
                cn += ` ${this.curMsg.customClass}`;
            } else {
                cn += ` ${this.curMsg.type}`;
            }
            if (this.inProgress) {
                cn += ' show-progress';
            }
            return cn;
        },
        total() {
            let total = this.msgs.length;
            if (this.curMsg !== false) {
                total++;
            }
            return total;
        },
        backdropVisible() {
            return (this.backdrop || this.curMsg.backdrop);
        },
        backdropProps() {
            let obj = {
                color: '#111111',
                opacity: '0.5',
                closeOnBlur: false
            };
            if (typeof this.backdrop === 'object') {
                obj = Object.assign(obj, this.backdrop);
            }
            if (typeof this.curMsg.backdrop === 'object') {
                obj = Object.assign(obj, this.curMsg.backdrop);
            }
            if (this.closeOnBlur || this.curMsg.closeOnBlur) {
                obj.closeOnBlur = true;
            }
            return obj;
        }
    },
    methods: {
        iconName(msg) {
            if (false === msg.icon) {
                return false;
            }
            if (msg.icon && msg.icon instanceof Array) {
                return msg.icon;
            }
            const icons = {
                info: ['fas', 'info-circle'],
                success: ['fas', 'check-circle'],
                danger: ['fas', 'exclamation-circle'],
                warn: ['fas', 'exclamation-triangle']
            };
            return icons[msg.type];
        },
        add(msg) {
            if (msg instanceof Array) {
                msg.forEach(msgItem => {
                    this.addMessage(msgItem);
                });
            } else {
                this.addMessage(msg);
            }
        },
        addMessage(msg) {
            msg = {
                title: '',
                message: '',
                type: 'success',
                autoClose: 0,
                customClass: '',
                icon: true,
                backdrop: false,
                closeOnBlur: false,
                ...msg
            };
            if (msg.message || msg.title) {
                this.msgs.push(msg);
                if (!this.curMsg) {
                    this.inProgress = false;
                    this.nextMessage();
                }
            }
        },
        removeMessage() {
            this.cancelAutoClose();
            this.isVisible = false;
            setTimeout(() => {
                this.inProgress = false;
                this.$emit('removed', this.curMsg);
                this.curMsg = false;
                this.nextMessage();
            }, this.duration);
        },
        removeAll() {
            this.msgs = [];
            this.removeMessage();
        },
        nextMessage() {
            this.cancelAutoClose();
            if (this.msgs.length && !this.curMsg) {
                this.curMsg = this.msgs.shift();
                setTimeout(() => {
                    this.isVisible = true;
                    if (this.curMsg.autoClose) {
                        setTimeout(() => {
                            if (this.progressBar) {
                                this.inProgress = true;
                            }
                            this.setAutoClose();
                        }, this.duration);
                    }
                }, 100);
            }
        },
        hoverToast(isOver) {
            if (this.progressBar && this.curMsg.autoClose) {
                if (isOver) {
                    this.cancelAutoClose();
                } else {
                    this.setAutoClose();
                }
                this.inProgress = !isOver;
            }
        },
        cancelAutoClose() {
            try { clearTimeout(this.autoCloseHandler); }
            catch(e) { e }
        },
        setAutoClose() {
            this.cancelAutoClose();
            this.autoCloseHandler = setTimeout(() => this.removeMessage(), Number(this.curMsg.autoClose));
        },
        clickBackdrop() {
            if (this.backdropProps.closeOnBlur) {
                this.removeMessage();
            }
        }
    },
    beforeMount() {
        // The code below makes One Toast accessible to all components.
        // After One Toast is included, you can use this.$oneToast in any place.
        Vue.prototype.$oneToast = {
            add: msg => this.add(msg),
            remove: () => this.removeMessage(),
            clear: () => this.removeAll()
        };
        this.$emit('oneToastAvailable');
    }
}
</script>

<template>
    <div class="container">
        <div ref="wrapper" :class="cssClass">
            <transition :name="transition" appear>
                <div 
                    ref="toast" 
                    :class="toastClass" 
                    v-show="isVisible"
                    @mouseenter="hoverToast(true)"
                    @mouseleave="hoverToast(false)"
                >
                    <div class="content">
                        <div class="icon" v-if="curMsg && iconVisible && iconName(curMsg)">
                            <font-awesome-icon :icon="iconName(curMsg)" />
                        </div>
                        <div class="message">
                            <strong class="title" v-if="curMsg.title" v-html="curMsg.title"></strong>
                            <span v-if="curMsg.message" v-html="curMsg.message"></span>
                        </div>
                        <a class="close-button" @click.prevent="removeMessage">
                            <font-awesome-icon icon="times" />
                        </a>
                    </div>
                    <div 
                        v-if="progressBar && curMsg.autoClose" 
                        class="progress-bar" 
                        :style="`--time: ${curMsg.autoClose}ms`"
                    >
                        <div></div>
                    </div>
                </div>
            </transition>
            <transition name="fade" v-if="showCounterBadge">
                <div class="counter-badge" v-if="msgs.length">{{ msgs.length }}</div>
            </transition>
        </div>
        <div class="one-toast-backdrop" @click="clickBackdrop"></div>
        <embed-style :rules="cssRules" />
    </div>
</template>

<style lang="scss">
    @import './one-toast.scss';
</style>