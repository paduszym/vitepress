<template>
  <div v-if="url" class="page-edit">
    <a class="link" :href="url" target="_blank" rel="noopener noreferrer">
      {{ text }} <OutboundLink class="icon" />
    </a>
  </div>
</template>

<script lang="ts">
import { defineComponent, computed } from 'vue'
import { useSiteDataByRoute, usePageData } from 'vitepress'
import { isNullish, endingSlashRE, isExternal } from '../utils'
import OutboundLink from './icons/OutboundLink.vue'

function createEditLink(
  repo: string,
  docsRepo: string,
  docsDir: string,
  docsBranch: string,
  path: string
) {
  const bitbucket = /bitbucket.org/
  if (bitbucket.test(repo)) {
    const base = isExternal(docsRepo) ? docsRepo : repo
    return (
      base.replace(endingSlashRE, '') +
      `/src` +
      `/${docsBranch}/` +
      (docsDir ? docsDir.replace(endingSlashRE, '') + '/' : '') +
      path +
      `?mode=edit&spa=0&at=${docsBranch}&fileviewer=file-view-default`
    )
  }

  const base = isExternal(docsRepo)
    ? docsRepo
    : `https://github.com/${docsRepo}`
  return (
    base.replace(endingSlashRE, '') +
    `/edit` +
    `/${docsBranch}/` +
    (docsDir ? docsDir.replace(endingSlashRE, '') + '/' : '') +
    path
  )
}

export default defineComponent({
  components: {
    OutboundLink
  },

  setup() {
    const site = useSiteDataByRoute()
    const page = usePageData()

    const url = computed(() => {
      const showEditLink = isNullish(page.value.frontmatter.editLink)
        ? site.value.themeConfig.editLinks
        : page.value.frontmatter.editLink

      if (!showEditLink) {
        return null
      }

      const {
        repo,
        docsDir = '',
        docsBranch = 'master',
        docsRepo = repo
      } = site.value.themeConfig

      const { relativePath } = page.value

      if (relativePath && repo) {
        return createEditLink(
          repo,
          docsRepo || repo,
          docsDir,
          docsBranch,
          relativePath
        )
      }

      return null
    })

    const text = computed(() => {
      return site.value.themeConfig.editLinkText || 'Edit this page'
    })

    return {
      url,
      text
    }
  }
})
</script>

<style scoped>
.page-edit {
  padding-top: 1rem;
  padding-bottom: 1rem;
  overflow: auto;
}

.link {
  display: flex;
  align-items: center;
  font-weight: 500;
  color: var(--text-color-light);
  transition: color .25s;
}

.link:hover {
  text-decoration: none;
  color: var(--accent-color);
}

.icon {
  display: block;
  margin-top: 2px;
  margin-left: 8px;
  width: 16px;
  height: 16px;
}
</style>
