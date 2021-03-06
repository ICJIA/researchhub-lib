<template>
  <BaseCard
    id="dataset-view"
    :external="dataset.external"
    :project="dataset.project"
  >
    <v-row class="mx-0 px-6 py-4">
      <tag :is="smAndDown ? 'h3' : 'h2'" :class="smAndDown ? 'pb-2' : ''">
        <span class="small" style="color: #666">Datasets</span>
        <v-icon>$vuetify.icons.chevronRight</v-icon>
        <template>{{ dataset.title }}</template>
      </tag>

      <v-spacer></v-spacer>

      <v-row justify="end" no-gutters>
        <v-dialog v-model="dialog" class="text-center" persistent width="500">
          <template #activator="{ on }">
            <v-btn :small="smAndDown" text v-on="on">
              <template>{{ 'Download' }}</template>
              <v-icon>$vuetify.icons.download</v-icon>
            </v-btn>
          </template>

          <v-sheet class="font-lato">
            <h3 class="pa-6">Did you read the metadata?</h3>
            <p class="px-6 pb-6">{{ msgDialog }}</p>

            <v-row class="mx-0 px-3 pb-3" justify="end">
              <v-btn text @click="downloadHelper">Yes, download</v-btn>

              <v-btn text @click="dialog = false">Back</v-btn>
            </v-row>
          </v-sheet>
        </v-dialog>

        <BaseButton
          label="Back"
          :small="smAndDown"
          :to="preview ? '' : '/datasets'"
        >
          <template>{{ 'back' }}</template>
        </BaseButton>
      </v-row>
    </v-row>

    <v-divider></v-divider>

    <div
      class="px-6 pb-6"
      :class="dataset.external || dataset.project ? 'pt-0' : 'pt-6'"
    >
      <MarkerExternal v-if="dataset.external" />
      <MarkerProject v-else-if="dataset.project" />

      <h2 class="mb-4 font-weight-light">About this dataset</h2>

      <v-row no-gutters>
        <v-col cols="12" md="6" lg="4" class="mr-sm-4">
          <BasePropDisplay name="Updated">
            <template>{{ dataset.date }}</template>
          </BasePropDisplay>

          <BasePropDisplay name="Sources">
            <span v-for="(source, i) in dataset.sources" :key="i">
              <template v-if="i > 0">{{
                dataset.sources.length > i + 1 ? ', ' : ' and '
              }}</template>

              <a
                v-if="source.url"
                :href="source.url"
                target="_blank"
                rel="noreferrer"
              >
                {{ source.title }}
              </a>
              <template v-else>{{ source.title }}</template>
            </span>
          </BasePropDisplay>

          <BasePropDisplay v-if="dataset.categories" name="Categories">
            <span>{{ dataset.categories }}</span>
          </BasePropDisplay>

          <BasePropDisplay v-if="dataset.tags" name="Tags">
            <BasePropChip
              v-for="tag in dataset.tags"
              :key="tag"
              @chip-click="$emit('tag-click', $event)"
            >
              <template>{{ tag }}</template>
            </BasePropChip>
          </BasePropDisplay>

          <BasePropDisplay v-if="dataset.timeperiod" name="Time period">
            <template>{{ dataset.timeperiod }}</template>
          </BasePropDisplay>
        </v-col>

        <v-col cols="12" md="6" lg="4">
          <BasePropDisplay v-if="dataset.unit" name="Unit of analysis">
            <span class="text-capitalize">{{ dataset.unit }}</span>
          </BasePropDisplay>

          <BasePropDisplay
            v-if="dataset.description"
            name="Description"
            :dense="true"
          >
            <span>{{ dataset.description }}</span>
          </BasePropDisplay>

          <BasePropDisplay v-if="dataset.notes" name="Notes">
            <ul>
              <li v-for="note in dataset.notes" :key="note">
                <template>{{ note }}</template>
              </li>
            </ul>
          </BasePropDisplay>
        </v-col>
      </v-row>

      <div v-if="dataset.variables" class="hidden-sm-and-down py-6">
        <h2 class="mb-4 font-weight-light">Variables</h2>
        <div ref="variables" class="variables-table font-lato small"></div>
      </div>

      <BaseInfoBlock v-if="dataset.funding">
        <template #title>{{ 'Funding acknowledgment' }}</template>
        <template #text>{{ dataset.funding }}</template>
      </BaseInfoBlock>

      <BaseInfoBlock v-if="dataset.citation">
        <template #title>{{ 'Suggested citation' }}</template>
        <template #text>
          <!-- eslint-disable-next-line vue/no-v-html -->
          <span v-html="dataset.citation"></span>
        </template>
      </BaseInfoBlock>

      <BaseInfoBlock v-if="hasRelated">
        <template #title>{{ 'Related contents' }}</template>
        <template #text>
          <ul class="font-lato">
            <li v-for="(app, i) in dataset.apps" :key="`app${i}`">
              <router-link :to="preview ? '' : `/apps/${app.slug}`">
                <template>{{ `[APP] ${app.title}` }}</template>
              </router-link>
            </li>
            <li v-for="(article, i) in dataset.articles" :key="`article${i}`">
              <router-link :to="preview ? '' : `/articles/${article.slug}`">
                <template>{{ `[ARTICLE] ${article.title}` }}</template>
              </router-link>
            </li>
          </ul>
        </template>
      </BaseInfoBlock>
    </div>
  </BaseCard>
</template>

<script>
import { format } from './utils/itemFormatter'
import BaseButton from './components/BaseButton'
import BaseCard from './components/BaseCard'
import BaseInfoBlock from './components/BaseInfoBlock'
import BasePropChip from './components/BasePropChip'
import BasePropDisplay from './components/BasePropDisplay'
import MarkerExternal from './components/MarkerExternal'
import MarkerProject from './components/MarkerProject'

const arr2table = ({ arr, cols = ['name', 'type', 'definition', 'values'] }) =>
  `<table>${getThead({ cols })}${getTbody({ cols, rows: arr })}</table>`

const getRow = (row, cols) =>
  `<tr>${cols.map(col => `<td>${row[col] ? row[col] : ''}</td>`).join('')}</tr>`

const getTbody = ({ rows, cols }) =>
  `<tbody>${rows.map(row => getRow(row, cols)).join('')}</tbody>`

const getThead = ({ cols }) =>
  `<thead><tr>${cols
    .map(col => `<th>${col[0].toUpperCase()}${col.slice(1)}</th>`)
    .join('')}</tr></tbody>`

export default {
  components: {
    BaseButton,
    BaseCard,
    BaseInfoBlock,
    BasePropChip,
    BasePropDisplay,
    MarkerExternal,
    MarkerProject
  },
  props: {
    item: {
      type: Object,
      default() {
        return {
          articles: null,
          apps: null,
          categories: null,
          citation: null,
          date: null,
          description: null,
          external: null,
          funding: null,
          notes: null,
          project: null,
          sources: null,
          tags: null,
          timeperiod: null,
          title: null,
          variables: null,
          unit: null
        }
      }
    },
    downloader: {
      type: Function,
      default() {}
    },
    preview: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      dialog: false,
      msgDialog:
        'Context matters. Please read and understand the metadata shown in this page before downloading and using the dataset.'
    }
  },
  computed: {
    dataset() {
      return format(this.item)
    },
    hasRelated() {
      const { apps, articles } = this.item
      return (apps && apps.length) || (articles && articles.length)
    },
    smAndDown() {
      return this.$vuetify.breakpoint.smAndDown
    }
  },
  mounted() {
    const { variables } = this.item
    if (variables)
      this.$refs.variables.innerHTML = arr2table({ arr: variables })
  },
  methods: {
    async downloadHelper() {
      await this.downloader()
      this.dialog = false
    }
  }
}
</script>
