<template>
    <span class="embed-style" style="display: none" v-html="cssText"></span>
</template>

<script>
export default {
    name: 'EmbedStyle',
    props: {
        rules: {
            type: Array,
            default() {
                return []
            }
        }
    },
    computed: {
        cssText() {
            let txt = ' ';
            this.rules.forEach(rule => {
                txt += `${rule.selector} { `;
                for (const prop in rule.props) {
                    txt += `${this.fromCamelCase(prop)}: ${rule.props[prop]}; `;
                }
                txt += ' } ';
            })
            return `<style>${txt}</style>`;
        }
    },
    methods: {
        fromCamelCase(str) {
            let newStr = '';
            for (let l = 0; l < str.length; l++) {
                if (/[A-Z]/.test(str.charAt(l))) {
                    const lower = str.charAt(l).toLowerCase();
                    newStr += `-${lower}`;
                } else {
                    newStr += str.charAt(l);
                }
            }
            return newStr;
        }
    }
}
</script>