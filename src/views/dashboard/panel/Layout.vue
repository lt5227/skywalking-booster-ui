<!-- Licensed to the Apache Software Foundation (ASF) under one or more
contributor license agreements.  See the NOTICE file distributed with
this work for additional information regarding copyright ownership.
The ASF licenses this file to You under the Apache License, Version 2.0
(the "License"); you may not use this file except in compliance with
the License.  You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License. -->
<template>
  <grid-layout
    v-model:layout="dashboardStore.layout"
    :col-num="24"
    :row-height="10"
    :is-draggable="dashboardStore.editMode"
    :is-resizable="dashboardStore.editMode"
    v-if="dashboardStore.layout.length"
  >
    <grid-item
      v-for="item in dashboardStore.layout"
      :x="item.x"
      :y="item.y"
      :w="item.w"
      :h="item.h"
      :i="item.i"
      :key="item.i"
      @click="clickGrid(item)"
      :class="{ active: dashboardStore.activedGridItem === item.i }"
      :drag-ignore-from="dragIgnoreFrom"
    >
      <component :is="item.type" :data="item" />
    </grid-item>
  </grid-layout>
  <div class="no-data-tips" v-else>{{ t("noWidget") }}</div>
</template>
<script lang="ts">
import { defineComponent, onBeforeUnmount } from "vue";
import { useI18n } from "vue-i18n";
import { useDashboardStore } from "@/store/modules/dashboard";
import { useSelectorStore } from "@/store/modules/selectors";
import { LayoutConfig } from "@/types/dashboard";
import controls from "../controls/index";
import { dragIgnoreFrom } from "../data";

export default defineComponent({
  name: "Layout",
  components: { ...controls },
  setup() {
    const { t } = useI18n();
    const dashboardStore = useDashboardStore();
    const selectorStore = useSelectorStore();

    function clickGrid(item: LayoutConfig) {
      dashboardStore.activeGridItem(item.i);
      dashboardStore.selectWidget(item);
      if (item.type === "Tab") {
        dashboardStore.setActiveTabIndex(0);
      }
    }
    onBeforeUnmount(() => {
      dashboardStore.setLayout([]);
      selectorStore.setCurrentService(null);
      selectorStore.setCurrentPod(null);
      dashboardStore.setEntity("");
      dashboardStore.setConfigPanel(false);
    });
    return {
      dashboardStore,
      clickGrid,
      t,
      dragIgnoreFrom,
    };
  },
});
</script>
<style lang="scss" scoped>
.vue-grid-layout {
  background: #f7f9fa;
  height: auto !important;
}

.vue-grid-item:not(.vue-grid-placeholder) {
  background: #fff;
  box-shadow: 0px 1px 4px 0px #00000029;
  border-radius: 3px;
}

.vue-grid-item.active {
  border: 1px solid #409eff;
}

.no-data-tips {
  width: 100%;
  text-align: center;
  font-size: 14px;
  padding-top: 30px;
  color: #888;
}
</style>
