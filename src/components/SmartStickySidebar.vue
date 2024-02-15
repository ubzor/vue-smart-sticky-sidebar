<template>
    <div ref="containerRef" :class="containerClass" :style="{ position: 'relative', minHeight: `${contentHeight}px` }">
        <div ref="contentRef" :class="contentClass" :style="contentStyles">
            <slot />
        </div>
    </div>
</template>

<script lang="ts" setup>
import { computed, ref, toRefs, onMounted, onBeforeUnmount, type HTMLAttributes } from 'vue'
import { useRafFn, useElementBounding, useWindowScroll, useWindowSize } from '@vueuse/core'

enum StickyStates {
    StickTocontainerTop = 'stick-to-parent-top',
    StickTocontainerBottom = 'stick-to-parent-bottom',
    StickToScreenTop = 'stick-to-screen-top',
    StickToScreenBottom = 'stick-to-screen-bottom',
    StickToSomePoint = 'stick-to-some-point'
}

const props = withDefaults(
    defineProps<{
        containerClass?: any
        contentClass?: any
        topSpacing?: number
        bottomSpacing?: number
    }>(),
    {
        containerClass: '',
        contentClass: '',
        topSpacing: 0,
        bottomSpacing: 0
    }
)

const { containerClass, contentClass, topSpacing, bottomSpacing } = toRefs(props)

const containerRef = ref<HTMLElement>()
const contentRef = ref<HTMLElement>()

const {
    width: containerWidth,
    height: containerHeight,
    top: containerTop,
    bottom: containerBottom
} = useElementBounding(containerRef)

const { height: contentHeight, top: contentTop, bottom: contentBottom } = useElementBounding(contentRef)

const { y: windowScroll } = useWindowScroll()

const { height: windowHeight } = useWindowSize()

const mutationObserver = ref<MutationObserver>()

const currentStickyState = ref<StickyStates>(StickyStates.StickToScreenTop)
const lastcontentOffset = ref(contentTop.value - containerTop.value)
const lastWindowScrollPosition = ref(windowScroll.value)
const currentcontainerOffset = ref(0)

const contentStyles = computed(
    () =>
        ({
            [StickyStates.StickTocontainerTop]: {
                position: 'relative'
            },
            [StickyStates.StickTocontainerBottom]: {
                position: 'absolute',
                bottom: 0,
                width: '100%'
            },
            [StickyStates.StickToScreenTop]: {
                position: 'fixed',
                top: `${topSpacing.value}px`,
                width: `${containerWidth.value}px`
            },
            [StickyStates.StickToScreenBottom]: {
                position: 'fixed',
                bottom: `${bottomSpacing.value}px`,
                width: `${containerWidth.value}px`
            },
            [StickyStates.StickToSomePoint]: {
                position: 'absolute',
                top: `${lastcontentOffset.value}px`,
                width: '100%'
            }
        }[currentStickyState.value] as HTMLAttributes['style'])
)

const contentFitscontainerSize = computed(
    () => contentHeight.value <= windowHeight.value - topSpacing.value - bottomSpacing.value
)

const updatecontentStatus = () => {
    if (containerHeight.value <= contentHeight.value || containerTop.value >= topSpacing.value) {
        currentStickyState.value = StickyStates.StickTocontainerTop
        return
    }

    if (
        currentStickyState.value === StickyStates.StickTocontainerBottom &&
        contentBottom.value - contentHeight.value - topSpacing.value > 0
    ) {
        currentStickyState.value = StickyStates.StickToScreenTop
        return
    }

    if (contentFitscontainerSize.value) {
        if (contentHeight.value + topSpacing.value <= containerBottom.value) {
            currentStickyState.value = StickyStates.StickToScreenTop
            return
        }

        currentStickyState.value = StickyStates.StickTocontainerBottom
        return
    }

    if (contentTop.value < containerTop.value) {
        currentStickyState.value = StickyStates.StickTocontainerTop
        return
    }

    if (contentBottom.value > containerBottom.value) {
        currentStickyState.value = StickyStates.StickTocontainerBottom
        return
    }

    if (
        containerHeight.value + currentcontainerOffset.value + bottomSpacing.value <
        windowScroll.value + windowHeight.value
    ) {
        currentStickyState.value = StickyStates.StickTocontainerBottom
        return
    }

    if (
        currentStickyState.value === StickyStates.StickToScreenBottom &&
        lastcontentOffset.value > contentTop.value - containerTop.value
    ) {
        currentStickyState.value = StickyStates.StickToSomePoint
        return
    }

    if (contentBottom.value <= windowHeight.value - bottomSpacing.value) {
        currentStickyState.value = StickyStates.StickToScreenBottom
        return
    }

    if (contentTop.value > topSpacing.value) {
        currentStickyState.value = StickyStates.StickToScreenTop
        return
    }

    if (
        currentStickyState.value === StickyStates.StickToScreenTop &&
        lastcontentOffset.value < contentTop.value - containerTop.value
    ) {
        currentStickyState.value = StickyStates.StickToSomePoint
        return
    }
}

useRafFn(() => {
    currentcontainerOffset.value =
        (containerRef.value?.getBoundingClientRect().top ?? 0) - document.body.getBoundingClientRect().top

    updatecontentStatus()

    lastWindowScrollPosition.value = windowScroll.value
    lastcontentOffset.value = contentTop.value - containerTop.value
})

onMounted(() => {
    mutationObserver.value = new MutationObserver(() => {
        // console.log('slot content changed')
    })

    if (contentRef.value) {
        mutationObserver.value.observe(contentRef.value, {
            childList: true,
            subtree: true
        })
    }
})

onBeforeUnmount(() => {
    mutationObserver.value?.disconnect()
})
</script>
