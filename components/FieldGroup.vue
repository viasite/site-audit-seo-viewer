<template>
  <div class="field-group" :id="'filter-' + group.name">
    <!-- group header -->
    <div class="field-group__header">
      <input
        type="checkbox"
        :checked="groupChecked"
        @click="setPreset({name: 'all', columns: [...[$store.state.defaultField],...group.fields.map(f => f.name)]});"
        :title="'Columns:\n' + group.fields.map(f => f.comment).join('\n')"
      >

      <el-dropdown>
        <span class="el-dropdown-link" @click="changeGroupOpened">
          <span class="field-group__name">{{ group.name }}</span>
          <i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item
            v-for="preset in columnPresets"
            :key="'columnPreset' + preset.name"
            v-if="isGroupNameInPreset"
          >
            <ColumnPresetButton :preset="preset" @click="setPreset(preset);"></ColumnPresetButton>
          </el-dropdown-item>
          <el-dropdown-item
            v-for="preset in filterPresets"
            :key="preset.name"
            v-if="isGroupNameInPreset"
          >
            <FilterPresetButton :preset="preset"></FilterPresetButton>
          </el-dropdown-item>
          <el-dropdown-item v-for="field in group.fields" :key="field.name">
            <ColumnField
              :field="field"
              :checked="$store.getters.fieldExists(field)"
              @click="$emit('toggleField', field, false, true)"
              :class="{ active: $store.getters.fieldExists(field) }"
            ></ColumnField>
          </el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </div>

    <!-- selected-fields -->
    <div class="field-group__selected-fields">
      <ColumnField
        :field="field"
        :checked="$store.getters.fieldExists(field)"
        v-for="field in group.fields"
        :key="field.name"
        @click="$emit('toggleField', field, false, true)"
        :class="{ active: $store.getters.fieldExists(field) }"
        v-if="$store.getters.fieldExists(field) && !opened"
      ></ColumnField>
    </div>

    <!-- group content -->
    <div :class="{'field-group__content': true, collapse: !opened}">
      <span
        v-for="preset in columnPresets"
        :key="'columnPreset' + preset.name"
        v-if="isGroupNameInPreset"
      >
        <ColumnPresetButton :preset="preset" @click="setPreset(preset);"></ColumnPresetButton>
      </span>

      <span
        v-for="preset in filterPresets"
        :key="preset.name"
        v-if="isGroupNameInPreset"
      >
        <FilterPresetButton :preset="preset"></FilterPresetButton>
      </span>

      <ColumnField
        :field="field"
        :checked="$store.getters.fieldExists(field)"
        @click="$emit('toggleField', field, false, true)"
        :class="{ 'available-fields__field': true, active: $store.getters.fieldExists(field) }"
        v-for="field in group.fields"
        :key="'content' + field.name"
      ></ColumnField>
    </div>
  </div>
</template>

<script>
import FilterPresetButton from "~/components/FilterPresetButton";
import ColumnPresetButton from "~/components/ColumnPresetButton";
import ColumnField from "~/components/ColumnField";

export default {
  components: { FilterPresetButton, ColumnPresetButton, ColumnField },
  props: ["group", "opened"],
  data() {
    return {};
  },

  computed: {
    isGroupNameInPreset() {
      return this.preset && this.preset.groups && this.preset.groups.indexOf(this.group.name) !== -1;
    },

    columnPresets() {
      return this.$store.state.columnPresets;
    },
    filterPresets() {
      return this.$store.state.filterPresets;
    },
    groupChecked() {
      let checked = true;
      this.group.fields.forEach(field => {
        if (!this.$store.getters.fieldExists(field)) checked = false;
      });
      return checked;
    }
  },

  methods: {
    setPreset(preset) {
      this.$emit("setPreset", preset);
    },

    changeGroupOpened() {
      this.$emit("changeGroupOpened");
    },
  }
};
</script>
