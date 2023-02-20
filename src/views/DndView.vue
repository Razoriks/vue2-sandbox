<template>
  <div class="container about">
    <div class="grid-selector">
      <button-grid
        v-for="grid in grids"
        :key="grid.name"
        @click="changeGrid(grid)"
        :class="{ 'btn-grid_active': activeGrid == grid }"
      >
        {{ grid.title }}
      </button-grid>
    </div>

    <div class="camera-desk" :class="activeGrid.styleClass">
      <div
        class="camera-big"
        :class="{ 'camera-big_filled': camerasBig.length > 0 }"
        @drop="onDrop($event, 'big')"
        @dragenter.prevent
        @dragover.prevent
      >
        <div class="camera-big__caption">
          Drag & drop camera to here for resize & sort
        </div>
        <div
          v-for="camera in camerasBig"
          :key="camera.id"
          class="camera-item"
          draggable="true"
          @dragstart="startDrag($event, camera)"
          @drop.stop="onDrop($event, 'camera', camera)"
        >
          <div class="square"></div>
          {{ camera.id }}
        </div>
      </div>

      <div
        v-for="camera in cameras"
        :key="camera.id"
        class="camera-item"
        draggable="true"
        @dragstart="startDrag($event, camera)"
      >
        <div class="square"></div>
        {{ camera.id }}
      </div>
    </div>
  </div>
</template>

<script>
// Data
import camerasArray from '@/data/cameras'
import gridsArray from '@/data/grids'

// Components
import ButtonGrid from '@/components/ButtonGrid.vue'

export default {
  data: function () {
    return {
      count: 0,
      activeGrid: {},
      grids: gridsArray,
      cameras: camerasArray,
      camerasBig: [],
    }
  },
  beforeMount() {
    this.activeGrid = this.grids.find((el) => el.default)
    this.camerasBig = this.cameras.filter((el) => el.active)
    this.cameras = this.cameras.filter((el) => !el.active)
    this.resetGrid()
  },
  methods: {
    startDrag(event, item) {
      event.dataTransfer.effectAllowed = 'move'
      event.dataTransfer.dropEffect = 'move'
      event.dataTransfer.setData('cameraId', item.id)
    },
    onDrop(event, targetName, targetObject = null) {
      const cameraId = event.dataTransfer.getData('cameraId')

      if (targetName == 'big') {
        const camera = this.cameras.find((camera) => camera.id === cameraId)

        if (camera) {
          this.camerasBig.push(camera)
          this.cameras = this.cameras.filter((camera) => camera.id !== cameraId)
        }
      }
      if (targetName == 'base') {
        const camera = this.camerasBig.find((camera) => camera.id === cameraId)
        if (camera) {
          this.cameras.push(camera)
          this.camerasBig = this.camerasBig.filter(
            (camera) => camera.id !== cameraId
          )
        }
      }
      if (targetName == 'camera') {
         const camera = this.cameras.find((camera) => camera.id === cameraId)

        if (camera) {
          this.camerasBig.push(camera)
          this.cameras.push(targetObject)
          this.cameras.splice(this.cameras.indexOf(camera), 1)
          this.camerasBig.splice(this.camerasBig.indexOf(targetObject), 1)
        }
      }
      this.resetGrid()
    },
    resetGrid() {
      let toGrid = this.grids.find(
        (el) => el.smallCameras == this.cameras.length
      )
      if (toGrid) {
        this.activeGrid = toGrid
      }
    },
    changeGrid(grid) {
      this.activeGrid = grid
    },
  },
  components: {
    ButtonGrid,
  },
}
</script>

<style lang="scss">
* {
  box-sizing: border-box;
  transition: 0.3s;
}
body {
  margin: 0;
}
.container {
  padding: 0 16px;
  width: 1240px;
  margin: 0 auto;
}

.camera {
  &-wrapper {
    display: flex;
    background-color: #dedede;
    padding: 16px;
    border-radius: 10px;
    gap: 16px;
  }

  &-item {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 200px;
    border: 2px dashed #42b983;
    color: #42b983;
    background-color: #fff;
    font-size: 26px;
    font-weight: bold;
    border-radius: 10px;
    flex-grow: 1;
    padding: 16px;
    cursor: pointer;

    position: relative;
    .square {
      position: absolute;
      cursor: pointer;
      background-color: #fff;
      width: 30px;
      height: 30px;
      top: 0;
      right: 0;
    }
  }
}

.camera-big {
  border: 2px dashed #42b983;
  background-color: #42b983;
  border-radius: 10px;
  padding: 16px;
  display: flex;
  align-items: center;
  text-align: center;

  &__caption {
    color: #ffffff;
    flex-grow: 1;
  }

  .camera-item {
    height: 100%;
  }
}

.camera-big_filled {
  .camera-big__caption {
    display: none;
  }
}

.camera-desk {
  display: grid;
  gap: 16px;

  &_sm-r {
    grid-template-columns: repeat(6, 1fr);
    grid-template-rows: 1fr;

    .camera-big {
      grid-row-start: 1;
      grid-row-end: 6;
      grid-column-start: 1;
      grid-column-end: 6;
    }
  }

  &_sm-br {
    // grid-template-columns: 5fr 1fr;
    grid-template-columns: repeat(6, 1fr);
    grid-template-rows: 1fr;

    .camera-wrapper {
      flex-direction: column;
    }

    .camera-big {
      grid-row-start: 1;
      grid-row-end: 5;
      grid-column-start: 1;
      grid-column-end: 6;
    }
  }

  &_sm-b4r2 {
    grid-template-columns: repeat(12, 1fr);
    grid-template-rows: 1fr;

    .camera-wrapper {
      flex-direction: column;
    }

    .camera-big {
      grid-row-start: 1;
      grid-row-end: 3;
      grid-column-start: 1;
      grid-column-end: 10;
    }

    .camera-item {
      grid-column: span 3;

      &:nth-of-type(2),
      &:nth-of-type(3) {
        grid-column-start: 10;
        grid-column-end: 13;
      }
    }
  }
  &_sm-b3r2 {
    grid-template-columns: repeat(12, 1fr);
    grid-template-rows: 1fr;

    .camera-wrapper {
      flex-direction: column;
    }

    .camera-big {
      grid-row-start: 1;
      grid-row-end: 3;
      grid-column-start: 1;
      grid-column-end: 9;
    }

    .camera-item {
      grid-column: span 4;

      &:nth-of-type(2),
      &:nth-of-type(3) {
        grid-column-start: 9;
        grid-column-end: 13;
      }
    }
  }
}

.grid-selector {
  display: flex;
  margin-bottom: 16px;
}

@media (max-width: 640px) {
  .container {
    width: 100%;
  }

  .camera {
    &-wrapper {
      flex-direction: column;
    }

    &-item {
    }
  }
}
</style>
