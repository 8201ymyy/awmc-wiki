<script setup lang="ts">
import { computed, nextTick, onBeforeUnmount, onMounted, ref, watch } from 'vue'
import { useData } from 'vitepress'
import 'swagger-ui-dist/swagger-ui.css'

type SwaggerUIInstance = { destroy?: () => void }

const { lang } = useData()
const isEnglish = computed(() => lang.value === 'en-US')
const selected = ref('gateway')
const mountPoint = ref<HTMLElement | null>(null)
let ui: SwaggerUIInstance | null = null

const services = computed(() => [
  { id: 'gateway', label: isEnglish.value ? 'AWMC Gateway API' : 'AWMC 网关 API', url: '/openapi/gateway.json' },
  { id: 'chart', label: isEnglish.value ? 'Chart Review API' : '谱面评价 API', url: '/openapi/chart.json' },
  { id: 'status', label: 'Status API', url: '/openapi/status.json' },
  { id: 'assets', label: 'Assets API', url: '/openapi/assets.json' },
  { id: 'net', label: 'AWMCNET API', url: 'https://net.wmc.pub/openapi.json' }
])

const currentService = computed(() => services.value.find(service => service.id === selected.value) ?? services.value[0])

async function renderSwagger() {
  if (!mountPoint.value) return
  ui?.destroy?.()
  mountPoint.value.replaceChildren()

  const { default: SwaggerUI } = await import('swagger-ui-dist/swagger-ui-es-bundle.js')
  ui = SwaggerUI({
    domNode: mountPoint.value,
    url: currentService.value.url,
    deepLinking: true,
    displayRequestDuration: true,
    filter: true,
    tryItOutEnabled: false,
    docExpansion: 'list',
    persistAuthorization: false,
    requestSnippetsEnabled: true,
    syntaxHighlight: { activate: true, theme: 'agate' }
  }) as SwaggerUIInstance
}

watch(selected, async () => {
  await nextTick()
  await renderSwagger()
})

onMounted(renderSwagger)
onBeforeUnmount(() => ui?.destroy?.())
</script>

<template>
  <div class="swagger-docs">
    <div class="service-toolbar">
      <label for="swagger-service">{{ isEnglish ? 'API service' : 'API 服务' }}</label>
      <select id="swagger-service" v-model="selected">
        <option v-for="service in services" :key="service.id" :value="service.id">
          {{ service.label }}
        </option>
      </select>
      <a :href="currentService.url" target="_blank" rel="noopener noreferrer">
        {{ isEnglish ? 'OpenAPI schema' : 'OpenAPI 规范' }}
      </a>
    </div>

    <div class="key-notice">
      {{ isEnglish
        ? 'Credentials are isolated by service and are cleared when switching services.'
        : '各服务的凭证相互独立；切换服务时会清除已填写的凭证。' }}
    </div>

    <div ref="mountPoint" class="swagger-mount" />
  </div>
</template>

<style scoped>
.swagger-docs {
  margin-top: 20px;
}

.service-toolbar {
  display: flex;
  align-items: center;
  gap: 12px;
  flex-wrap: wrap;
  padding: 12px 0;
  border-top: 1px solid var(--vp-c-divider);
  border-bottom: 1px solid var(--vp-c-divider);
}

.service-toolbar label {
  font-weight: 600;
}

.service-toolbar select {
  min-width: 220px;
  height: 36px;
  padding: 0 32px 0 10px;
  border: 1px solid var(--vp-c-border);
  border-radius: 6px;
  color: var(--vp-c-text-1);
  background: var(--vp-c-bg);
}

.service-toolbar a {
  margin-left: auto;
  font-size: 14px;
}

.key-notice {
  margin: 12px 0 4px;
  color: var(--vp-c-text-2);
  font-size: 14px;
}

.swagger-mount {
  min-height: 480px;
}

@media (max-width: 640px) {
  .service-toolbar {
    align-items: stretch;
  }

  .service-toolbar label,
  .service-toolbar select {
    width: 100%;
  }

  .service-toolbar a {
    margin-left: 0;
  }
}
</style>

<style>
.swagger-docs .swagger-ui,
.swagger-docs .swagger-ui .info .title,
.swagger-docs .swagger-ui .opblock-tag,
.swagger-docs .swagger-ui .opblock .opblock-summary-description,
.swagger-docs .swagger-ui .opblock-description-wrapper,
.swagger-docs .swagger-ui .response-col_status,
.swagger-docs .swagger-ui table thead tr td,
.swagger-docs .swagger-ui table thead tr th {
  color: var(--vp-c-text-1);
  font-family: var(--vp-font-family-base);
}

.swagger-docs .swagger-ui .scheme-container,
.swagger-docs .swagger-ui .opblock-body pre,
.swagger-docs .swagger-ui select,
.swagger-docs .swagger-ui input,
.swagger-docs .swagger-ui textarea {
  background: var(--vp-c-bg-soft);
  color: var(--vp-c-text-1);
}

.swagger-docs .swagger-ui .info {
  margin: 24px 0;
}

.swagger-docs .swagger-ui .wrapper {
  max-width: none;
  padding: 0;
}

.swagger-docs .swagger-ui .topbar {
  display: none;
}

.swagger-docs .swagger-ui .scheme-container {
  box-shadow: none;
  border: 1px solid var(--vp-c-divider);
}
</style>
