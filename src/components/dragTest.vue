<template>
    <div class="drag-wrapper">
        <div class="dropzone">
            <div 
                v-for="(item, index) in tempList" 
                :key="item.id+'-'+index"
                :id="item.id"
                draggable="true"
                @dragstart="startDrag($event, item, index)"
                @dragend="dragLeave()"
                @drop="dragEnd($event, index)"
                @dragover.prevent="enterCheck(true)"
                @dragenter.prevent
                @dragleave="enterCheck(false)"
                class="drag-box"
                :class="{ 
                    'is-dragging': sIsDragging && (item.id === sDraggingId),
                    // 'move-action-down': isMoveActionDwon(sDraggingId, sOverId) && isOver(item.id),
                    // 'move-action-up': isMoveActionUp(sDraggingId, sOverId) && isOver(item.id),
                }"
            >
                <!-- <div v-if="sOverShow && (item.id === sOverId) && (item.id !== sDraggingId)" class="move-point" /> -->
                <span>{{ item.title }}</span>
            </div>
            <div 
                v-if="this.tempList.length === 0"
                draggable
                @dragstart="startDrag($event, item, 0)"
                @dragend="dragLeave()"
                @drop="dragEnd($event, 0)"
                @dragover.prevent
                @dragenter.prevent
                class="drag-box is-dragging"></div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'dragTest',
    props: {
        pList: [],
        pDropzoneId: Number,
    },
    data() {
        return { 
            tempList: [],
            sIsDragging: false,
            sDraggingId: null,
            sOverId: null,
            sFrom: -1,
            sTo: -1,
            sOverShow: false,
            sCustomDiv: null,
            sListId: null,
            sEnterDragzone: false,
        }
    },
    methods: {
        startDrag(event, aItem, aIdx) {
            console.log('start drag dropzone', this.pDropzoneId);
            event.dataTransfer.dropEffect = 'move';
            event.dataTransfer.effectAllowed = 'move';
            event.dataTransfer.setData('itemId', aItem.id);

            // ghost image control
            let sCustomDiv = event.target.cloneNode(true);
            sCustomDiv.style.opacity = 0.01;
            sCustomDiv.id = 'customdiv';
            sCustomDiv.className = "drag-box "+aIdx+" "+aItem.id;
            document.body.appendChild(sCustomDiv);
            event.dataTransfer.setDragImage(sCustomDiv, event.offsetX, event.offsetY);
            this.sCustomDiv = sCustomDiv;

            this.sDraggingId = aItem.id;
            this.sFrom = aIdx;
            this.toggleDragging('over');
        },
        dragEnd(event, aTo) {
            event.preventDefault();
            console.log('drag end dropzone', this.pDropzoneId);

            const sCustomDiv = document.getElementById('customdiv').cloneNode(true);
            sCustomDiv.id = sCustomDiv.className.split(' ')[2];

            this.moveItem(sCustomDiv.className.split(' ')[1], aTo, sCustomDiv);

            this.sFrom = -1;
            this.sTo = -1;
            this.toggleDragging('end');
            this.endOverShow();
            if(this.sCustomDiv){
                document.body.removeChild(this.sCustomDiv);
                this.sCustomDiv = null;
            }
        },
        dragLeave() {
            if(this.sCustomDiv){
                document.body.removeChild(this.sCustomDiv);
                //여기서 삭제해버리자~
                if(this.sEnterDragzone) {
                    this.tempList.splice(this.sFrom, 1);
                }
                this.sCustomDiv = null;
            }
            this.sFrom = -1;
            this.sTo = -1;
            this.toggleDragging('end');
            this.endOverShow();
        },
        moveItem(aFrom, aTo, aCustomdiv) {
            if(this.sIsDragging) {
                this.tempList.splice(aTo, 0, this.tempList.splice(aFrom, 1)[0]);
            } else {
                this.tempList.splice(aTo, 0, {
                    id: aCustomdiv.id,
                    title: aCustomdiv.innerText,
                    list: this.pDropzoneId
                });
            }
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
        isMoveActionDwon(aFrom, aTo) {
            if(aFrom > aTo){
                return true;
            }
            return false;
        },
        isMoveActionUp(aFrom, aTo) {
            if(aFrom < aTo){
                return true;
            }
            return false;
        },
        isOver(aId) {
            // console.log('isOver func = ', this.sOverId, this.sDraggingId)
            return this.sOverShow && (aId === this.sOverId) && (aId !== this.sDraggingId);
        },
        enterCheck(aBool) {
            this.sEnterDragzone = aBool;
        }
    },
    created() {
        return this.tempList = this.pList
    }
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

    position: relative;
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
    border: 1px dashed black;
    background-color: lightgray;
    > span {
        display: none;
    }
}
.move-point {
    position: absolute;
    top: -5px;
    width: 100px;
    height: 5px;
    background-color: aqua;
}

.move-action-down {
    transform: translate(0, 70px);
    transition: transform 0.3s ease-in-out;
}
.move-action-up {
    transform: translate(0, -70px);
    transition: transform 0.3s ease-in-out;
}
.ghost-image {
    width: 100px;
    height: 50px;
    border: 1px solid black;
    background-color: red;
}
@keyframes slowAction {
}
</style>