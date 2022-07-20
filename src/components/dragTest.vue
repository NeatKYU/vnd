<template>
    <div class="drag-wrapper">
        <!-- <span>dropzone 1</span> -->
        <!-- <div 
            @drop="onDrop($event, 1)"
            @dragover.prevent
            @dragenter.prevent
            class="dropzone"
        > -->
        <div
            class="dropzone"
        >
            <div 
                v-for="(item, index) in tempListOne" 
                :key="item.id" 
                draggable="true"
                @dragstart="startDrag($event, item, index)"
                @drop="dragEnd($event, index)"
                @dragover.prevent="overShow(item.id)"
                @dragenter.prevent
                class="drag-box"
                :class="{ 'is-dragging': sIsDragging && (item.id === sDraggingId) }"
            >
                <div v-if="sOverShow && (item.id === sOverId) && (item.id !== sDraggingId)" class="move-point" />
                {{ item.title }}
            </div>
        </div>
        <!-- <div class="divider-vertical" /> -->
        <!-- <span>dropzone 2</span> -->
        <!-- <div 
            @drop="onDrop($event, 2)" 
            @dragover.prevent
            @dragenter.prevent
            class="dropzone"
        >
            <div 
                v-for="item in tempListTwo" 
                :key="item.id" 
                draggable="true"
                @dragstart="startDrag($event, item)"
                class="drag-box"
                :class="{ 'is-dragging': sIsDragging && (item.id === sDraggingId) }"
            >
                {{ item.title }}
            </div>
        </div> -->
    </div>
</template>

<script>
export default {
    name: 'dragTest',
    data() {
        return { 
            tempListOne: [
                {
                    id: '1',
                    title: 'item A',
                    list: 1,
                },{
                    id: '2',
                    title: 'item B',
                    list: 1,
                },{
                    id: '3',
                    title: 'item C',
                    list: 1,
                },
            ],
            // tempListTwo: [],
            sIsDragging: false,
            sDraggingId: null,
            sOverId: null,
            sDragzone: 0,
            sFrom: -1,
            sTo: -1,
            sOverShow: false,
        }
    },
    computed: {
        // cFilterList(aListIndex) {
        //     return this.items.filter((aItem) => aItem.list === aListIndex);
        // },
    },
    methods: {
        startDrag(event, aItem, aIdx) {
            event.dataTransfer.dropEffect = 'move';
            event.dataTransfer.effectAllowed = 'move';
            event.dataTransfer.setData('itemId', aItem.id);
            this.sDraggingId = aItem.id;
            this.sDragzone = aItem.list;
            this.sFrom = aIdx;
            console.log(this.sDraggingId)
            this.toggleDragging('over');
        },
        // onDrop(event, aList) {
        //     const sItemId = event.dataTransfer.getData('itemId');
        //     // const sItem = this.items.filter((aItem) => aItem.id === sItemId);
        //     sItem[0].list = aList;
        //     this.sDraggingId = null;
        //     this.toggleDragging('end');
        // },
        dragEnd(event, aTo) {
            event.preventDefault();
            console.log('from to = ', this.sFrom, aTo)
            this.moveItem(this.sFrom, aTo);
            this.sFrom = -1;
            this.sTo = -1;
            this.toggleDragging('end');
            this.endOverShow();
        },
        moveItem(aFrom, aTo) {
            this.tempListOne.splice(aTo, 0, this.tempListOne.splice(aFrom, 1)[0]);
        },
        toggleDragging(aType){
            if(aType === 'over'){
                this.sIsDragging = true;
            } else {
                this.sIsDragging = false;
            }
        },
        overShow(aId) {
            this.sOverId = aId;
            this.sOverShow = true;
        },
        endOverShow() {
            this.sOverId = null;
            this.sOverShow = false;
        },
        filterList(aListIndex) {
            return this.items.filter((aItem) => aItem.list === aListIndex);
        },
    },
}
</script>

<style lang="scss" scoped>
.drag-wrapper {
    display: flex;
    justify-content: space-around;
}
.drag-box {
    width: 100px;
    height: 50px;
    border-radius: 10px;
    margin: 15px 5px;
    box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
    background-color: #fff;

    border: 1px solid;

    position: relative;
}
.dropzone {
    min-width: 50px;
    min-height: 100px;
    padding: 10px;
    background-color: rgb(180, 180, 180);
    
    animation: all 0.5s ease-in-out;
}
.divider {
    width: 100%;
    height: 1px;
    margin: 20px 0;
}

.divider-vertical {
    height: 100%;
    width: 2px;
    margin: 0 auto;
}

.is-dragging {
    border-color: red;
}
.move-point {
    position: absolute;
    top: -5px;
    width: 100px;
    height: 5px;
    background-color: aqua;
}
@keyframes slowAction {
}
</style>