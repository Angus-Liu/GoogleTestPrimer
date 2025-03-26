<template>
    <div class="grid grid-cols-[2fr_1fr] w-full h-full">
        <div class="slidev-layout default">
            <slot name="left" />
        </div>
        <div class="w-full h-full">
            <slot name="right" />
        </div>
    </div>
</template>