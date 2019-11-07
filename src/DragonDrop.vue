<script>
export default {
    props: {
        value: {
            type: Array,
            required: true
        },

        itemKey: {
            type: String,
            default: 'id'
        },

        tag: {
            type: String,
            default: 'span'
        },

        transition: {
            type: String,
            default: 'dragon-drop'
        }
    },

    methods: {

        // we derive the key by attempting to find it using the supplied 'itemKey'
        // if that is not found, we use the entire item as a key
        keyFn(item) {
            if (this.value.length && this.value[0] && this.value[0][this.itemKey]) {
                return item[this.itemKey];
            } else {
                return item;
            }
        },

        // the drop handler
        drop(i, e) {
            e.preventDefault();
            // restore grabbed key
            const y = JSON.parse(e.dataTransfer.getData('text'));

            // copy array & swap elements
            const newArr = [ ...this.value ];
            const index = newArr.indexOf(newArr.find(j => this.keyFn(j) === y));
            const index2 = newArr.indexOf(newArr.find(j => this.keyFn(j) === this.keyFn(i)));
            const rows = [newArr[Math.min(index, index2)], newArr[Math.max(index, index2)]];
            newArr.splice(Math.min(index, index2), 1, rows[1]);
            newArr.splice(Math.max(index, index2), 1, rows[0]);

            // emit update
            this.$emit('input', newArr);
            this.$emit('drag-end', { item: i });
        },

        dragStart(i, e) {
            e.dataTransfer.setData('text', JSON.stringify(this.keyFn(i)));
            this.$emit('drag-start', { item: i });
        },

        dragOver(e) {
            e.preventDefault();

        }
    },
    render(h) {
        return h('transition-group', {attrs: { name: this.transition, tag: this.tag } }, [
            ...this.value.map(item => {
                // pre-compile the slot
                // ------
                // [0] only takes the first root element
                // instead of taking [0], we should be able to map
                // over all slots to remove the one root element
                // restriction.
                const slot = this.$scopedSlots.default({ item })[0];

                // key generation
                const key = this.keyFn(item);

                // default dragon events. these are native html events
                // which are first processed and emit the matching
                // vue.js event
                const dragonEvents = {
                    drop: e => this.drop(item, e),
                    dragover: this.dragOver,
                    dragstart: e => this.dragStart(item, e)
                };

                // if the tag exists we can inject the event listeners & handlers
                // if no tag exists, we assume its raw text and we wrap with a
                // div so that we can add our events/handlers
                return slot.tag ? {
                    ...slot,
                    key,
                    data: {
                        ...slot.data,
                        on: {
                            ...slot.data && slot.data.on,
                            ...dragonEvents
                        },
                        attrs: {
                            ...slot.data && slot.data.attrs,
                            draggable: true
                        }
                    }
                } : [h('div', { // we need to wrap raw text with a div so that we can attach event listeners etc.
                    key,
                    on: dragonEvents,
                    attrs: { draggable: true },
                }, slot.text)];
            })
        ]);
    }
};
</script>
