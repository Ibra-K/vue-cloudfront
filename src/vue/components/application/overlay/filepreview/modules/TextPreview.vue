<template>
    <div class="text-preview">

        <div v-for="(line, index) of lines" class="line">
            <span class="index">{{ index }}</span>
            <pre class="content">{{ line }}</pre>
        </div>

        <p v-if="!lines.length">{{ msg }}</p>

    </div>
</template>

<script>

    export default {
        props: {
            url: {
                type: String,
                required: true
            }
        },

        data() {
            return {
                lines: [],
                msg: null
            };
        },

        watch: {
            url(newUrl) {
                this.updateContent(newUrl);
            }
        },

        mounted() {
            this.updateContent(this.url);
        },

        methods: {
            updateContent(url) {
                fetch(url).then(response => {
                    if (response.ok) {
                        return response.text();
                    } else {
                        throw response;
                    }
                }).then(text => {
                    text = text.trim();

                    if (text.length > 50000) {
                        this.msg = 'File is too big';
                    } else if (!text.length) {
                        this.msg = 'File is empty';
                    } else {
                        this.lines = text.trim().split(/\r?\n/g);
                    }

                }).catch(() => 0);
            }
        }
    };

</script>

<style lang="scss" scoped>

    .text-preview {
        max-height: 100%;
        @include fixed-width(90%);
        overflow-y: auto;

        > p {
            height: 2em;
        }

        .line {
            @include flex(row);
            flex-wrap: nowrap;
            overflow: hidden;
            word-break: break-word;
            word-break: break-all;
            font-size: 0.95em;

            .index,
            .content {
                font-family: monospace;
                font-size: 1em;
            }

            .index {
                display: inline-block;
                min-width: 2em;
                margin-right: 1.5em;
                color: rgba($palette-deep-blue, 0.25);
                text-align: right;
            }

            .content {
                width: 100%;
                white-space: pre-wrap;
                user-select: all;
                cursor: text;
            }
        }
    }

</style>
