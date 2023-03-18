<template>
    <div id="app">
        <h1>One Toast <span>for Vue 2.x</span></h1>
        <div class="big">A component to display toast messages in your Vue app</div>

        <p>
            One Toast was designed to display only one message at a time, even if there is a queue of messages to be displayed
        </p>

        <form action="#" @submit.prevent="">
            <h3>Message configuration</h3>
            <div class="formline">
                <label for="title">Title</label>
                <input type="text" id="title" placeholder="An optional title here" v-model="msg.title">
            </div>
            <div class="formline">
                <label for="message">Message</label>
                <textarea id="message" v-model="msg.message" spellcheck="false"></textarea>
            </div>
            <div class="formline">
                <label for="type">Message type</label>
                <div class="inline">
                    <input type="radio" name="type" id="type-success" value="success" v-model="msg.type">
                    <label for="type-success">success</label>
                </div>
                <div class="inline">
                    <input type="radio" name="type" id="type-info" value="info" v-model="msg.type">
                    <label for="type-info">info</label>
                </div>
                <div class="inline">
                    <input type="radio" name="type" id="type-warn" value="warn" v-model="msg.type">
                    <label for="type-warn">warn</label>
                </div>
                <div class="inline">
                    <input type="radio" name="type" id="type-danger" value="danger" v-model="msg.type">
                    <label for="type-danger">danger</label>
                </div>
                <div class="inline">
                    <input type="radio" name="type" id="type-blank" value="blank" v-model="msg.type">
                    <label for="type-blank">blank</label>
                </div>
            </div>
            <div class="formline">
                <label for="autoClose">Auto close time</label>
                <input 
                    type="range" 
                    id="autoClose" 
                    :min="0" 
                    :max="20000" 
                    :step="500" 
                    v-model.number="msg.autoClose"
                >
                <span class="autoclose-time">{{ autoCloseValue }}</span>
            </div>
            <div class="formline inline">
                <input type="checkbox" id="msgBackdrop" v-model="msg.backdrop">
                <label for="msgBackdrop">Use backdrop</label>
            </div>
            <div class="formline inline">
                <input type="checkbox" id="closeOnBlurMsg" v-model="msg.closeOnBlur">
                <label for="closeOnBlurMsg">Close on blur (click in backdrop)</label>
            </div>
            <div class="formline buttons">
                <button @click="addToast" class="primary" :disabled="addButtonDisabled">Add toast</button>
                <button @click="removeToast">Remove toast</button>
                <button @click="removeAll">Remove all</button>
            </div>
            <h3>Global options</h3>
            <div class="formline inline">
                <input type="checkbox" id="useIcon" v-model="oneToastCfg.useIcon">
                <label for="useIcon">Icon visible</label>
            </div>
            <div class="formline inline">
                <input type="checkbox" id="progressBar" v-model="oneToastCfg.progressBar">
                <label for="progressBar">Progress bar</label>
            </div>
            <div class="formline inline">
                <input type="checkbox" id="showCounterBadge" v-model="oneToastCfg.showCounterBadge">
                <label for="showCounterBadge">Counter badge</label>
            </div>
            <div class="formline">
                <label for="duration">Animation duration</label>
                <input 
                    type="range" 
                    id="duration" 
                    :min="100" 
                    :max="2000" 
                    :step="50" 
                    v-model.number="oneToastCfg.duration"
                >
                <span class="duration-time">{{ oneToastCfg.duration }} milliseconds</span>
            </div>
            <div class="formline">
                <label for="positionX">Position X</label>
                <div class="inline">
                    <input type="radio" name="positionX" id="positionX-left" value="left" v-model="oneToastCfg.positionX">
                    <label for="positionX-left">left</label>
                </div>
                <div class="inline">
                    <input type="radio" name="positionX" id="positionX-center" value="center" v-model="oneToastCfg.positionX">
                    <label for="positionX-center">center</label>
                </div>
                <div class="inline">
                    <input type="radio" name="positionX" id="positionX-right" value="right" v-model="oneToastCfg.positionX">
                    <label for="positionX-right">right</label>
                </div>
            </div>
            <div class="formline">
                <label for="positionY">Position Y</label>
                <div class="inline">
                    <input type="radio" name="positionY" id="positionY-top" value="top" v-model="oneToastCfg.positionY">
                    <label for="positionY-top">top</label>
                </div>
                <div class="inline">
                    <input type="radio" name="positionY" id="positionY-center" value="center" v-model="oneToastCfg.positionY">
                    <label for="positionY-center">center</label>
                </div>
                <div class="inline">
                    <input type="radio" name="positionY" id="positionY-bottom" value="bottom" v-model="oneToastCfg.positionY">
                    <label for="positionY-bottom">bottom</label>
                </div>
            </div>
            <div class="formline inline">
                <input type="checkbox" id="backdrop" v-model="oneToastCfg.backdrop">
                <label for="backdrop">Use backdrop</label>
            </div>
            <div class="formline inline">
                <input type="checkbox" id="closeOnBlur" v-model="oneToastCfg.closeOnBlur">
                <label for="closeOnBlur">Close on blur (click in backdrop)</label>
            </div>
        </form>
        <OneToast v-bind="oneToastCfg" @oneToastAvailable="initialMessage" />
    </div>
</template>

<script>
import OneToast from '@/components/OneToast'

export default {
    name: 'App',
    components: {
        OneToast
    },
    data() {
        return {
            msg: {
                type: 'info',
                title: '',
                message: 'Message content goes here',
                autoClose: 0,
                backdrop: false,
                closeOnBlur: false
            },
            oneToastCfg: {
                positionX: 'left',
                positionY: 'bottom',
                duration: 350,
                useIcon: true,
                progressBar: true,
                showCounterBadge: true,
                backdrop: false,
                closeOnBlur: false
            }
        }
    },
    computed: {
        addButtonDisabled() {
            return (!this.msg.title && !this.msg.message);
        },
        autoCloseValue() {
            if (this.msg.autoClose > 0) {
                return this.msg.autoClose + ' milliseconds';
            }
            return "disabled";
        }
    },
    methods: {
        addToast() {
            this.$oneToast.add(this.msg)
        },
        removeToast() {
            this.$oneToast.remove()
        },
        removeAll() {
            this.$oneToast.clear()
        },
        initialMessage() {
            this.$oneToast.add([
                {
                    title: 'Hey you!',
                    message: '<em>Welcome to One Toast component!</em><br>Use the form to configure and show some toast messages.',
                    type: 'blank',
                    customClass: 'custom-toast',
                    autoClose: 10000,
                    icon: ['far', 'star']
                }
            ])
        }
    }
}
</script>

<style lang="scss">
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;

    h1 {
        font-family: 'Josefin Sans', sans-serif;
        font-size: 50px;
        margin-bottom: 0.5rem;
        span {
            font-weight: 400;
            font-size: 32px;
        }
    }

    .big {
        display: block;
        font-size: 18px;
        margin: -10px auto 2rem;
    }

    form {
        width: 740px;
        max-width: 100%;
        margin: 2rem auto;
        text-align: left;
        .formline {
            margin: 1rem auto;
            display: flex;
            flex-direction: row;
            align-items: center;
            &.inline,
            .inline {
                display: inline-block;
                margin-right: 1.5rem;
                width: auto;
            }
            label {
                width: 25%;
                flex-grow: 0;
                flex-shrink: 0;
            }
            input[type="text"], select, textarea {
                width: 70%;
                flex-grow: 2;
                flex-shrink: 2;
                padding: 0.45rem;
                border: 1px solid #cccccc;
                font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
                outline: 0;
                &:focus {
                    border: 1px dashed #666;
                }
            }
            textarea {
                height: 1rem;
                transition: height 300ms ease-in-out;
                &:focus {
                    height: 3rem;
                }
            }
            input[type="checkbox"] {
                width: auto;
                margin-right: 0.5rem;
                flex-grow: 0;
                flex-shrink: 0;
                &+ label {
                    width: 70%;
                    flex-grow: 2;
                    flex-shrink: 2;
                }
            }
            input[type="range"] {
                width: 40%;
                margin-right: 1rem;
            }
            &.buttons {
                justify-content: flex-end;
                button {
                    border-radius: 4px;
                    padding: 0.6rem 1.2rem;
                    border: 1px solid #cccccc;
                    margin-left: 0.5rem;
                    cursor: pointer;
                    &.primary {
                        background-color: #115daa;
                        color: #fff;
                    }
                    &:disabled {
                        opacity: 0.6;
                        cursor: default;
                    }
                }
            }
        }
    }
    .custom-toast {
        background-color: #666;
        color: #cfcfcf;
        .icon {
            opacity: 1;
        }
        svg path {
            fill: #ffffff;
        }
    }
}
</style>
