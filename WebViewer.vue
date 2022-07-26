<template>
    <div id="webviewer" ref="viewer"></div>
</template>

<script>
import { ref, onMounted } from 'vue'
import WebViewer from '@pdftron/webviewer'

export default {
    name: 'WebViewer',
    props: { iniatialDoc: { type: String }}, 
    setup(props){
        const viewer = ref(null);
        onMounted(() =>{
            const path = `$(process.env.BASE_URL)webviewer`;
            WebViewer({path, initialDoc: props.initialDoc}, viewer.value);
        })

        return { viewer }
    }
}
</script>

<style>
    #webviewer {
        height: 800 px;
    }
    
</style>