<template>
  <BaseCardLayout :external="dataset.external" :project="dataset.project">
    <template #title>
      <BaseTitleDisplay :to="preview ? '' : `/datasets/${dataset.slug}`">
        <template>{{ dataset.title }}</template>
      </BaseTitleDisplay>

      <div v-if="dataset.tags">
        <BasePropChip
          v-for="tag in dataset.tags"
          :key="tag"
          @chip-click="$emit('tag-click', $event)"
        >
          <template>{{ tag }}</template>
        </BasePropChip>
      </div>
    </template>

    <template #props>
      <BasePropDisplay name="Updated">
        <template>{{ dataset.date }}</template>
      </BasePropDisplay>

      <BasePropDisplay v-if="dataset.sources" name="Sources">
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
            <template>{{ source.title }}</template>
          </a>

          <template v-else>{{ source.title }}</template>
        </span>
      </BasePropDisplay>

      <BasePropDisplay v-if="dataset.categories" name="Categories">
        <span>{{ dataset.categories }}</span>
      </BasePropDisplay>
    </template>

    <template #buttons>
      <BaseButton
        label="More"
        :small="true"
        :to="preview ? null : `/datasets/${dataset.slug}`"
        icon="$vuetify.icons.dotsHorizontal"
      >
        <template>{{ 'more' }}</template>
      </BaseButton>
    </template>
  </BaseCardLayout>
</template>

<script>
import { format } from './utils/itemFormatter'
import BaseButton from './components/BaseButton'
import BaseCardLayout from './components/BaseCardLayout'
import BasePropChip from './components/BasePropChip'
import BasePropDisplay from './components/BasePropDisplay'
import BaseTitleDisplay from './components/BaseTitleDisplay'

export default {
  components: {
    BaseButton,
    BaseCardLayout,
    BasePropChip,
    BasePropDisplay,
    BaseTitleDisplay
  },
  props: {
    item: {
      type: Object,
      default() {
        return {
          articles: null,
          apps: null,
          categories: null,
          date: null,
          description: null,
          external: null,
          project: null,
          sources: null,
          tags: null,
          title: null
        }
      }
    },
    preview: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    dataset() {
      return format(this.item)
    }
  }
}
</script>
