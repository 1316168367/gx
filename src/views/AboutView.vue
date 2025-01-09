<template>
  <div>
    <div style="height: calc(100vh)">
      <RelationGraph
        ref="graphRef"
        :options="graphOptions"
        :on-node-click="onNodeClick"
        :on-line-click="onLineClick"
      >
        <template #graph-plug>
          <!-- To facilitate understanding, the CSS style code is directly embedded here. -->
          <div
            style="
              z-index: 300;
              position: absolute;
              left: 10px;
              top: calc(100% - 50px);
              font-size: 12px;
              background-color: #ffffff;
              border: #efefef solid 1px;
              border-radius: 10px;
              width: 260px;
              height: 40px;
              display: flex;
              align-items: center;
              justify-content: center;
            "
          >
            Legend：
            <div>
              More to one

              <div
                style="height: 5px; width: 80px; background-color: rgba(159, 23, 227, 0.65)"
              ></div>
            </div>
            <div style="margin-left: 10px">
              One to one

              <div
                style="height: 5px; width: 80px; background-color: rgba(29, 169, 245, 0.76)"
              ></div>
            </div>
          </div>
        </template>
        <template #canvas-plug>
          <!--- You can put some elements that are not allowed to be dragged here --->
        </template>
        <template #node="{ node }: any">
          <div :id="node.id" style="width: 300px; background-color: #f39930">
            <div>{{ node.text }}</div>
            <div class="fieldList">
              <div
                :id="column.id"
                class="fieldItem"
                style="width: 50%"
                v-for="column of node.data.fieldList"
                :key="column.columnName"
              >
                {{ column.fieldName }} {{ column.fieldComment }} {{ column.fieldType }}
              </div>
            </div>
          </div>
        </template>
      </RelationGraph>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { defineComponent, ref, onMounted } from 'vue'
import RelationGraph from 'relation-graph-vue3'
import type {
  RGOptions,
  RGNode,
  RGLine,
  RGLink,
  RGUserEvent,
  RGJsonData,
  RelationGraphComponent,
} from 'relation-graph-vue3'

const graphRef = ref<RelationGraphComponent | null>(null)
const graphOptions: RGOptions = {
  debug: false,
  allowSwitchLineShape: true,
  allowSwitchJunctionPoint: true,
  allowShowDownloadButton: true,
  defaultJunctionPoint: 'border',
  placeOtherNodes: false,
  placeSingleNode: false,
  graphOffset_x: -200,
  graphOffset_y: 100,
  defaultLineMarker: {
    markerWidth: 20,
    markerHeight: 20,
    refX: 3,
    refY: 3,
    data: 'M 0 0, V 6, L 4 3, Z',
  },
  layout: {
    layoutName: 'tree',
    from: 'left',
    min_per_width: 500,
    min_per_height: 500,
  },
  // You can refer to the parameters in "Graph" for setting here
}

onMounted(() => {
  showGraph()
})

const showGraph = async () => {
  // const tables = [
  //   { tableName: 'SYS_USER', tableComents: 'SYS_USER comments', x: 500, y: -300 },
  //   { tableName: 'SYS_DEPT', tableComents: 'SYS_DEPT comments', x: 0, y: -300 },
  //   { tableName: 'SYS_ROLE', tableComents: 'SYS_ROLE comments', x: 0, y: 0 },
  //   { tableName: 'SYS_USER_ROLE', tableComents: 'SYS_USER_ROLE comments', x: 500, y: 0 },
  //   { tableName: 'SYS_RESOURCE', tableComents: 'SYS_RESOURCE comments', x: 0, y: 300 },
  //   { tableName: 'SYS_ROLE_RESOURCE', tableComents: 'SYS_ROLE_RESOURCE comments', x: 500, y: 300 },
  // ]
  // const tableCols = [
  //   { tableName: 'SYS_USER', columnName: 'id', dataType: 'varchar(36)' },
  //   { tableName: 'SYS_USER', columnName: 'user_name', dataType: 'varchar(50)' },
  //   { tableName: 'SYS_USER', columnName: 'dept_id', dataType: 'varchar(36)' },
  //   { tableName: 'SYS_USER', columnName: 'create_time', dataType: 'TIMESTAMP' },
  //   { tableName: 'SYS_USER', columnName: 'status', dataType: 'varchar(1)' },
  //   { tableName: 'SYS_DEPT', columnName: 'id', dataType: 'varchar(36)' },
  //   { tableName: 'SYS_DEPT', columnName: 'dept_name', dataType: 'varchar(50)' },
  //   { tableName: 'SYS_DEPT', columnName: 'parent_dept_id', dataType: 'varchar(50)' },
  //   { tableName: 'SYS_DEPT', columnName: 'create_time', dataType: 'TIMESTAMP' },
  //   { tableName: 'SYS_DEPT', columnName: 'status', dataType: 'varchar(50)' },
  //   { tableName: 'SYS_ROLE', columnName: 'id', dataType: 'varchar(36)' },
  //   { tableName: 'SYS_ROLE', columnName: 'role_name', dataType: 'varchar(36)' },
  //   { tableName: 'SYS_ROLE', columnName: 'create_time', dataType: 'TIMESTAMP' },
  //   { tableName: 'SYS_USER_ROLE', columnName: 'id', dataType: 'varchar(36)' },
  //   { tableName: 'SYS_USER_ROLE', columnName: 'user_id', dataType: 'varchar(36)' },
  //   { tableName: 'SYS_USER_ROLE', columnName: 'role_id', dataType: 'varchar(36)' },
  //   { tableName: 'SYS_USER_ROLE', columnName: 'create_time', dataType: 'TIMESTAMP' },
  //   { tableName: 'SYS_USER_ROLE', columnName: 'status', dataType: 'varchar(36)' },
  //   { tableName: 'SYS_RESOURCE', columnName: 'id', dataType: 'varchar(36)' },
  //   { tableName: 'SYS_RESOURCE', columnName: 'resource_name', dataType: 'varchar(36)' },
  //   { tableName: 'SYS_RESOURCE', columnName: 'create_time', dataType: 'TIMESTAMP' },
  //   { tableName: 'SYS_ROLE_RESOURCE', columnName: 'id', dataType: 'varchar(36)' },
  //   { tableName: 'SYS_ROLE_RESOURCE', columnName: 'role_id', dataType: 'varchar(36)' },
  //   { tableName: 'SYS_ROLE_RESOURCE', columnName: 'resource_id', dataType: 'varchar(36)' },
  //   { tableName: 'SYS_ROLE_RESOURCE', columnName: 'status', dataType: 'varchar(1)' },
  // ]
  // const columnRelations = [
  //   {
  //     sourceTableName: 'SYS_USER',
  //     sourceColumnName: 'dept_id',
  //     type: 'MORE_TO_ONE',
  //     targetTableName: 'SYS_DEPT',
  //     targetColumnName: 'id',
  //   },
  //   {
  //     sourceTableName: 'SYS_DEPT',
  //     sourceColumnName: 'parent_dept_id',
  //     type: 'ONE_TO_ONE',
  //     targetTableName: 'SYS_DEPT',
  //     targetColumnName: 'id',
  //   },
  //   {
  //     sourceTableName: 'SYS_USER_ROLE',
  //     sourceColumnName: 'user_id',
  //     type: 'MORE_TO_ONE',
  //     targetTableName: 'SYS_USER',
  //     targetColumnName: 'id',
  //   },
  //   {
  //     sourceTableName: 'SYS_USER_ROLE',
  //     sourceColumnName: 'role_id',
  //     type: 'MORE_TO_ONE',
  //     targetTableName: 'SYS_ROLE',
  //     targetColumnName: 'id',
  //   },
  //   {
  //     sourceTableName: 'SYS_ROLE_RESOURCE',
  //     sourceColumnName: 'role_id',
  //     type: 'MORE_TO_ONE',
  //     targetTableName: 'SYS_ROLE',
  //     targetColumnName: 'id',
  //   },
  //   {
  //     sourceTableName: 'SYS_ROLE_RESOURCE',
  //     sourceColumnName: 'resource_id',
  //     type: 'MORE_TO_ONE',
  //     targetTableName: 'SYS_RESOURCE',
  //     targetColumnName: 'id',
  //   },
  // ]
  // const graphNodes = tables.map((table) => {
  //   const { tableName, tableComents, x, y } = table
  //   return {
  //     id: tableName,
  //     text: tableComents,
  //     x,
  //     y,
  //     nodeShape: 1,
  //     data: {
  //       // Costomer key have to in data

  //       columns: tableCols.filter((col) => col.tableName === table.tableName),
  //     },
  //   }
  // })
  // const graphLines = columnRelations.map((relation) => {
  //   return {
  //     from: relation.sourceTableName + '-' + relation.sourceColumnName, // HtmlElement id

  //     to: relation.targetTableName + '-' + relation.targetColumnName, // HtmlElement id

  //     color: relation.type === 'ONE_TO_ONE' ? 'rgba(29,169,245,0.76)' : 'rgba(159,23,227,0.65)',
  //     text: '',
  //     fromJunctionPoint: 'left',
  //     toJunctionPoint: 'lr',
  //     lineShape: 4,
  //     lineWidth: 3,
  //   }
  // })
  // graphLines.push({
  //   from: 'SYS_USER', // HtmlElement id
  //   to: 'SYS_DEPT', // HtmlElement id
  //   color: 'rgba(159,23,227,0.65)',
  //   text: '',
  //   fromJunctionPoint: 'left',
  //   toJunctionPoint: 'lr',
  //   lineShape: 4,
  //   lineWidth: 3,
  // })
  const graphJsonData: RGJsonData = {
    rootId: 'a',
    nodes: [
      {
        id: 'a',
        text: '1',
        nodeShape: 1,
        data: {
          // Costomer key have to in data
          tableName: 'XSJBXJ',
          explanatory: '学生基本数据子类',
          fieldList: [
            {
              id: 'f1',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
              fieldKey: 'PRI',
            },
            {
              id: 'f2',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
          ],
        },
      },
      {
        id: 'b',
        text: '1',
        nodeShape: 1,
        data: {
          // Costomer key have to in data
          tableName: 'XSJBXJ',
          explanatory: '学生基本数据子类',
          fieldList: [
            {
              id: 'f3',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f3',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f3',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f3',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f3',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f3',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f3',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f3',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
          ],
        },
      },
      {
        id: 'c',
        text: '1',
        nodeShape: 1,
        data: {
          // Costomer key have to in data
          tableName: 'XSJBXJ',
          explanatory: '学生基本数据子类',
          fieldList: [
            {
              id: 'f4',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f4',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f4',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f4',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f4',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f4',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f4',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f4',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f4',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f4',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f4',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f4',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
            {
              id: 'f4',
              fieldName: 'XH',
              fieldType: 'varchar(20)',
              fieldComment: '学号',
            },
          ],
        },
      },
    ],
    lines: [
      { from: 'a', to: 'b', lineShape: 4 },
      { from: 'a', to: 'c', lineShape: 4 },
    ],
    elementLines: [
      { from: 'f1', to: 'f3' },
      { from: 'f4', to: 'f3' },
    ],
  }
  const graphInstance = graphRef.value?.getInstance()
  if (graphInstance) {
    await graphInstance.setJsonData(graphJsonData)
    await graphInstance.moveToCenter()
    await graphInstance.zoomToFit()
  }
}

const onNodeClick = (nodeObject: RGNode, $event: RGUserEvent) => {
  console.log('onNodeClick:', nodeObject)
}

const onLineClick = (lineObject: RGLine, linkObject: RGLink, $event: RGUserEvent) => {
  console.log('onLineClick:', lineObject)
}
</script>

<style lang="scss">
.fieldList {
  display: flex;
  flex-direction: column;
  align-items: center;
  .fieldItem {
    height: 40px;
    line-height: 40px;
  }
}
::v-deep(.relation-graph) {
  .rel-node-shape-1 {
    overflow: hidden;
  }
}
.c-data-table {
  background-color: #ffffff;
  border-collapse: collapse;
  width: 100%;
}
.c-data-table td,
.c-data-table th {
  border: 1px solid #f39930;
  color: #333333;
  padding: 5px;
  padding-left: 20px;
  padding-right: 20px;
}
.c-data-table td div,
.c-data-table th div {
  background-color: #1da9f5;
  color: #ffffff;
  border-radius: 5px;
}
</style>
