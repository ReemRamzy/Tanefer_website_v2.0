<template>
  <div>
    <div v-if="truncated" class="overview-preview">
      {{ truncatedText }}
      <span v-if="shouldTruncate">...</span>
    </div>
    <div v-else class="overview-full" v-html="sanitizedHtml" />

    <button
      v-if="shouldTruncate"
      class="see-more-button"
      @click="toggleTruncated"
    >
      {{ truncated ? 'See More' : 'See Less' }}
    </button>
  </div>
</template>

<script>
import DOMPurify from 'dompurify'

export default {
  name: 'TruncatedOverview',
  props: {
    overviewHtml: {
      type: String,
      default: ''
    },
    maxLength: {
      type: Number,
      default: 120
    }
  },
  data () {
    return {
      truncated: true
    }
  },
  computed: {
    sanitizedHtml () {
      return DOMPurify.sanitize(this.overviewHtml)
    },
    plainText () {
      const tempEl = document.createElement('div')
      tempEl.innerHTML = this.sanitizedHtml
      return tempEl.textContent || tempEl.innerText || ''
    },
    truncatedText () {
      if (this.plainText.length > this.maxLength) {
        return this.plainText.slice(0, this.maxLength)
      }
      return this.plainText
    },
    shouldTruncate () {
      return this.plainText.length > this.maxLength
    }
  },
  methods: {
    toggleTruncated () {
      this.truncated = !this.truncated
    }
  }
}
</script>

  <style scoped>
  .overview-preview {
    overflow: hidden;
    text-overflow: ellipsis;
  }

 /* .overview-full {
  } */

  .see-more-button {
    color: #007BFF;
    margin-top: 8px;
    cursor: pointer;
  }
  </style>
