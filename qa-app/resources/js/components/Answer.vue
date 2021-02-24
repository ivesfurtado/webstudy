<script>
export default {
    props: ['answer'],

    data() {
        return {
            editing: false,
            body: this.answer.body,
            bodyHtml: this.answer.body_html,
            id: this.answer.id,
            questionId: this.answer.question_id,
            beforeEditCache: null
        }
    },

    computed: {
        isInvalid() {
            return this.body.length < 1;
        },

        endpoint() {
            return `${this.questionId}/answers/${this.id}`;
        }
    },

    methods: {
        destroy() {
            this.$toast.question("Are you sure about that?", 'Confirm', {
            timeout: 20000,
            close: false,
            overlay: true,
            displayMode: 'once',
            id: 'question',
            zindex: 999,
            position: 'center',
            buttons: [
                ['<button><b>YES</b></button>', (instance, toast) => {
                    axios.delete(this.endpoint)
                    .then(response => {
                        $(this.$el).fadeOut(500, () => {
                            this.$toast.success(response.data.message, "Success", { timeout: 3000 });
                        })
                    })
                    instance.hide({ transitionOut: 'fadeOut' }, toast, 'button');
        
                }, true],
                ['<button>NO</button>', function (instance, toast) {
        
                    instance.hide({ transitionOut: 'fadeOut' }, toast, 'button');
        
                }],
            ]
        });
        },

        edit() {
            this.beforeEditCache = this.body;
            this.editing = true;
        },

        cancel() {
            this.body = this.beforeEditCache;
            this.editing = false;
        },

        update() {
            axios.patch(this.endpoint, {
                body: this.body
            })
            .then(response => {
                this.editing = false;
                this.bodyHtml = response.data.body_html;
                this.$toast.success(response.data.message, "Success", { timeout: 3000 });
            })
            .catch(error => {
                this.$toast.error(error.response.data.message, "Error", { timeout: 3000 });
            });
        }
    }
}
</script>