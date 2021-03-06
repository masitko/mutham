<template>
    <card icon="copy"
        refresh
        scrollable
        search
        :title="displayTitle"
        :overlay="$refs.documents && $refs.documents.loading"
        @refresh="$refs.documents.get()"
        :collapsed="!open || isEmpty"
        ref="card"
        @expand="isEmpty
            ? $refs.card.collapse()
            : null"
        @query-update="query = $event"
        :badge="count"
        :controls="1">
        <card-control slot="control-1">
            <file-uploader :params="{ documentable_type: type, documentable_id: id }"
                @upload-successful="$refs.documents.get();"
                :url="uploadLink"
                multiple/>
        </card-control>
        <div class="wrapper has-padding-medium">
            <documents :id="id"
                :type="type"
                :query="query"
                @update="count = $refs.documents.count; $refs.card.resize()"
                ref="documents"/>
        </div>
    </card>
</template>

<script>

import { mapState } from 'vuex';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faCopy, faPlusSquare } from '@fortawesome/free-solid-svg-icons';
import Card from '../bulma/Card.vue';
import CardControl from '../bulma/CardControl.vue';
import Documents from './Documents.vue';
import FileUploader from '../filemanager/FileUploader.vue';

library.add(faCopy, faPlusSquare);

export default {
    name: 'DocumentsCard',

    components: {
        Card, CardControl, Documents, FileUploader,
    },

    props: {
        open: {
            type: Boolean,
            default: false,
        },
        id: {
            type: Number,
            required: true,
        },
        type: {
            type: String,
            required: true,
        },
        title: {
            type: String,
            default: '',
        },
    },

    data: () => ({
        query: '',
        count: 0,
    }),

    computed: {
        ...mapState('layout', ['isMobile']),
        isEmpty() {
            return this.count === 0;
        },
        uploadLink() {
            return route('core.documents.store');
        },
        displayTitle() {
            return !this.isMobile
                ? this.title || this.__('Documents')
                : null;
        },
    },

    watch: {
        count() {
            this.$refs.card.resize();
        },
    },
};

</script>

<style scoped>

    .wrapper {
        max-height: 500px;
        overflow-y: auto;
    }

</style>
