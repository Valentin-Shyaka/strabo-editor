<template>
    <div class="umo-comment-container">
       
        <div class="umo-toc-content umo-scrollbar">
            <slot name='comments'>
              </slot>
        </div>
        <div class="umo-toc-resize-handle" @mousedown="startResize"></div>
      </div>
    </template>
    
    <script setup lang="ts">
    const container = inject('container')
    const editor = inject('editor')
    
    defineEmits(['close'])
    
    
    const baseTocWidth = 320
    const isResizing = ref(false)
    const startX = ref(0)
    const initialWidth = ref(baseTocWidth)
    const umoPageContainer = document.querySelector(
      '.umo-page-container',
    ) as HTMLElement
    const startResize = (e: MouseEvent) => {
      if (!umoPageContainer) {
        return
      }
      isResizing.value = true
      startX.value = e.clientX
      initialWidth.value = parseInt(
        getComputedStyle(
          umoPageContainer?.querySelector('.umo-toc-container') as HTMLElement,
        ).width,
        10,
      )
      umoPageContainer.addEventListener('mousemove', resize)
      umoPageContainer.addEventListener('mouseup', stopResize)
    }
    
    const resize = (e: MouseEvent) => {
      if (isResizing.value) {
        const offsetX = e.clientX - startX.value
        const newWidth = initialWidth.value + offsetX
        const minWidth = baseTocWidth / 1.5
        const maxWidth = baseTocWidth * 2
        if (newWidth >= minWidth && newWidth <= maxWidth) {
          const tocContainer = umoPageContainer.querySelector(
            '.umo-toc-container',
          ) as HTMLElement
          tocContainer.style.width = `${newWidth}px`
        }
      }
    }
    
    const stopResize = () => {
      isResizing.value = false
      umoPageContainer.removeEventListener('mousemove', resize)
      umoPageContainer.removeEventListener('mouseup', stopResize)
    }
    </script>
    
    <style lang="less" scoped>
    .umo-comment-container {
      background-color: transparent;
      border-right: solid 1px var(--umo-border-color);
      width: 400px;
      height: 77vh;
      box-sizing: border-box;
      display: flex;
      margin-top: 20px;
      margin-right: 20px;
      flex-direction: column;
      position: sticky;
      .umo-toc-resize-handle {
        position: absolute;
        top: 0;
        right: -2px;
        width: 3px;
        height: 100%;
        opacity: 0.5;
        background-color: transparent;
        &:hover {
          background-color: var(--umo-primary-color);
          cursor: col-resize;
        }
      }
      .umo-toc-title {
        border-bottom: solid 1px var(--umo-border-color-light);
        display: flex;
        align-items: center;
        position: relative;
        padding: 10px 15px;
        .icon-toc {
          margin-right: 5px;
          font-size: 20px;
        }
        .umo-dialog__close {
          position: absolute;
          right: 15px;
          display: flex;
          align-items: center;
          justify-content: center;
        }
      }
      .umo-toc-content {
        flex: 1;
        display: flex;
        padding: 10px;
        flex-direction: column;
        .umo-toc-tree {
          --td-comp-size-m: 28px;
          --td-comp-paddingLR-xs: 8px;
          --td-comp-margin-xs: 0;
          --td-brand-color-light: var(--umo-button-hover-background);
          user-select: none;
          :deep(.umo-tree__empty) {
            height: 60px;
            font-size: 12px;
            flex: 1;
            display: flex;
            align-items: center;
            justify-content: center;
            color: var(--umo-text-color-light);
          }
          :deep(.umo-is-active) {
            font-weight: 400;
            color: var(--umo-primary-color);
          }
        }
      }
    }
    </style>