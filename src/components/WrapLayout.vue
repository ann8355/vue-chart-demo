<template>
  <n-breadcrumb v-if="showBreadcrumb" separator=">">
    <n-breadcrumb-item
      v-for="(breadcrumbItem, breadcrumbIndex) in breadcrumbItems"
      :key="breadcrumbIndex"
      class="font-semibold"
      :class="{
        'cursor-pointer': !!breadcrumbItem.href,
      }"
      :clickable="!!breadcrumbItem.href"
      :href="breadcrumbItem.href"
    >
      {{ breadcrumbItem.label }}
    </n-breadcrumb-item>
  </n-breadcrumb>
  <div class="-mx-4 flex relative mt-12">
    <div v-if="showGroupMenu" class="hidden lg:block flex-none w-fit px-4">
      <h2 class="text-primary-07 mb-12">{{ menuGroup.groupLabel }}</h2>
      <n-list hoverable clickable style="--n-color: initial">
        <router-link
          v-for="(menu, menuIndex) in menuGroup.children"
          :key="menuIndex"
          :to="{
            path: menu.href || '#',
            force: true,
          }"
          #="{ navigate, href }"
          custom
        >
          <n-list-item @click="() => navigate()">
            <span class="flex items-center font-bold text-base -mx-3">
              <n-icon class="text-primary-01 mr-2">
                <icon-fa-arrow-circle-right />
              </n-icon>
              {{ menu.label }}
            </span>
          </n-list-item>
        </router-link>
      </n-list>
    </div>
    <div class="grow px-4 overflow-x-auto">
      <h2 class="text-secondary-02 border-l-[3px] border-secondary-01 pl-3 mb-12">
        {{ last(stackViews)?.title }}
        <app-button v-if="stackViews.length > 1" class="ml-auto" secondary @click="popViewComponent">
          <n-icon class="mr-1">
            <icon-vaadin-arrow-backward />
          </n-icon>
          返回{{ stackViews[stackViews.length - 2]?.title }}
        </app-button>
      </h2>
      <app-global-loading>
        <component :is="last(stackViews)?.component" v-bind="last(stackViews)?.props" />
      </app-global-loading>
    </div>
  </div>
</template>

<script setup>
import { last } from 'lodash-es'
import { usePortal } from './usePortal'

const props = defineProps({
  showGroupMenu: {
    type: Boolean,
    default: () => true,
  },
  showBreadcrumb: {
    type: Boolean,
    default: () => true,
  },
})

const { breadcrumbItems, menuTitle, menuGroup } = usePortal()
const {
  stackViews,
  pushViewComponent,
  popViewComponent,
  replaceCurrentViewComponent,
  updateCurrentViewTitle,
  toFirstViewComponent,
} = useProvideStackView()
const slots = useSlots()

onMounted(() => {
  stackViews.value = [
    {
      title: toValue(menuTitle),
      component: defineComponent({
        setup() {
          return () => {
            return slots.default?.()
          }
        },
      }),
    },
  ]
})

defineExpose({
  popViewComponent,
  pushViewComponent,
  replaceCurrentViewComponent,
  updateCurrentViewTitle,
  toFirstViewComponent,
})
</script>
