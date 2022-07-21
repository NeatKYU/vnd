<template>
    <div class="drag-wrapper">
        <div
            id="list"
            class="dropzone"
            :sortable="{
                onUpdate: sortableMove
            }"
        >
            <div 
                v-for="(item, index) in tempListOne" 
                :key="item.id"
                draggable="true"
                @dragstart="startDrag($event, item, index)"
                @dragend="dragLeave()"
                @drop="dragEnd($event, index)"
                @dragover.prevent="overShow(item.id)"
                @dragenter.prevent="moveOver(index)"
                class="drag-box"
                :class="{ 
                    'is-dragging': sIsDragging && (item.id === sDraggingId),
                    // 'move-action-down': isMoveActionDwon(sDraggingId, sOverId) && isOver(item.id),
                    // 'move-action-up': isMoveActionUp(sDraggingId, sOverId) && isOver(item.id),
                }"
            >
                <div v-if="sOverShow && (item.id === sOverId) && (item.id !== sDraggingId)" class="move-point" />
                <span>{{ item.title }}</span>
            </div>
        </div>
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
                },{
                    id: '4',
                    title: 'item D',
                    list: 1,
                },{
                    id: '5',
                    title: 'item E',
                    list: 1,
                },
            ],
            tempListTwo: [],
            sIsDragging: false,
            sDraggingId: null,
            sOverId: null,
            sDragzone: 0,
            sFrom: -1,
            sTo: -1,
            sOverShow: false,
            sCustomDiv: null,
        }
    },
    methods: {
        sortableMove: function (aEvent) {
            this.tempListOne.splice(aEvent.newIndex, 0, this.tempListOne.splice(aEvent.oldIndex, 1)[0])
        },
        startDrag(event, aItem, aIdx) {
            event.dataTransfer.dropEffect = 'move';
            event.dataTransfer.effectAllowed = 'move';
            event.dataTransfer.setData('itemId', aItem.id);
            // ghost image control
            let sCustomDiv = event.target.cloneNode(true);
            sCustomDiv.style.opacity = 0.01;
            document.body.appendChild(sCustomDiv);
            event.dataTransfer.setDragImage(sCustomDiv, event.offsetX, event.offsetY);
            this.sCustomDiv = sCustomDiv

            this.sDraggingId = aItem.id;
            this.sDragzone = aItem.list;
            this.sFrom = aIdx;
            console.log(this.sDraggingId)
            this.toggleDragging('over');
        },
        dragEnd(event, aTo) {
            event.preventDefault();
            console.log('from to = ', this.sFrom, aTo)
            this.moveItem(this.sFrom, aTo);
            this.sFrom = -1;
            this.sTo = -1;
            this.toggleDragging('end');
            this.endOverShow();
            if(this.sCustomDiv){
                console.log(this.sCustomDiv);
                document.body.removeChild(this.sCustomDiv);
                this.sCustomDiv = null;
            }
        },
        dragLeave() {
            this.sFrom = -1;
            this.sTo = -1;
            this.toggleDragging('end');
            this.endOverShow();
            if(this.sCustomDiv){
                console.log(this.sCustomDiv);
                document.body.removeChild(this.sCustomDiv);
                this.sCustomDiv = null;
            }
        },
        moveItem(aFrom, aTo) {
            this.tempListOne.splice(aTo, 0, this.tempListOne.splice(aFrom, 1)[0]);
        },
        moveOver(aTo) {
            console.log('over from to !', this.sFrom, aTo)
            this.tempListOne.splice(aTo, 0, this.tempListOne.splice(this.sFrom, 1)[0]);
            this.sFrom = aTo;
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
            return this.sOverShow && (aId === this.sOverId) && (aId !== this.sDraggingId);
        }
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