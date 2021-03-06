<template>
  <BaseCardLayout
    :external="article.external"
    :horizontal="horizontal"
    :image="article.thumbnail"
    :preview="preview"
  >
    <template #title>
      <BaseTitleDisplay :to="preview ? '' : `/articles/${article.slug}`">
        <template>{{ article.title }}</template>
      </BaseTitleDisplay>

      <div v-if="article.tags">
        <BasePropChip
          v-for="tag in article.tags"
          :key="tag"
          @chip-click="$emit('tag-click', $event)"
        >
          <template>{{ tag }}</template>
        </BasePropChip>
      </div>
    </template>

    <template #props>
      <BasePropDisplay v-if="article.date" name="Updated">
        <template>{{ article.date }}</template>
      </BasePropDisplay>

      <BasePropDisplay v-if="article.authors" name="Authors">
        <span v-for="(author, i) in article.authors" :key="author.title">
          <template v-if="i > 0">{{
            article.authors.length > i + 1 ? ', ' : ' and '
          }}</template>
          <a @click="$emit('author-click', $event)">{{ author.title }}</a>
        </span>
      </BasePropDisplay>

      <BasePropDisplay v-if="article.categories" name="Categories">
        <span>{{ article.categories }}</span>
      </BasePropDisplay>
    </template>

    <template #buttons>
      <v-btn
        v-if="article.abstract && horizontal"
        :aria-label="showAbstract ? 'Hide abstract' : 'Show abstract'"
        small
        text
        @click="showAbstract = !showAbstract"
      >
        <template>{{ 'abstract' }}</template>
        <v-icon>{{ abstractIcon }}</v-icon>
      </v-btn>

      <BaseButton
        label="More"
        :small="true"
        :to="preview ? null : `/articles/${article.slug}`"
        icon="$vuetify.icons.dotsHorizontal"
      >
        <template>{{ 'more' }}</template>
      </BaseButton>
    </template>

    <template #extra>
      <v-slide-y-transition v-if="horizontal">
        <div v-show="showAbstract" class="pa-6">{{ article.abstract }}</div>
      </v-slide-y-transition>
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
    horizontal: {
      type: Boolean,
      default: true
    },
    item: {
      type: Object,
      default() {
        return {
          abstract: null,
          authors: null,
          categories: null,
          date: null,
          external: null,
          tags: null,
          title: null,
          thumbnail: null
        }
      }
    },
    onTagClick: {
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
      showAbstract: false
    }
  },
  computed: {
    article() {
      return format(this.item)
    },
    abstractIcon() {
      return this.showAbstract
        ? '$vuetify.icons.chevronUp'
        : '$vuetify.icons.chevronDown'
    }
  }
}
</script>
