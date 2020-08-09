<script>
import draggable from 'vuedraggable';

export default {
  components: {
    draggable
  },
  data() {
    return {
      sections: [],
      currentFocus: null,
    }
  },
  computed: {
    currentFocusActiveTab() {
      return this.currentFocus.tabs.filter( t => t.focus === true )[0];
    }
  },
  methods: {
    addSection() {
      this.sections.push({
        name: 'sample section',
        value: '',
        focus: false,
        design: {
          padding: '',
          margin: '',
          border: '',
        },
        rows: [],
        tabs: [
          {
            label: 'Design',
            focus: true,
            sections: [
              {
                label: 'Padding',
                toggled: false,
                onUpdate( section ) {
                  section.design.padding   = this.fields.map( a => ( a.name + '-' + a.value ) ).join(' ');
                },
                fields: [
                  {
                    type: 'range',
                    label: 'Padding Left',
                    name: 'pl',
                    value: 0,
                    checked: false,
                  }, {
                    type: 'range',
                    label: 'Padding Top',
                    name: 'pt',
                    value: 0,
                    checked: false,
                  }, {
                    type: 'range',
                    label: 'Padding Right',
                    name: 'pr',
                    value: 0,
                    checked: false,
                  }, {
                    type: 'range',
                    label: 'Padding Bottom',
                    name: 'pb',
                    value: 0,
                    checked: false,
                  }
                ]
              }, {
                label: 'Marging',
                toggled: false,
                onUpdate( section ) {
                  section.design.margin   = this.fields.map( a => ( a.name + '-' + a.value ) ).join(' ');
                },
                fields: [
                  {
                    type: 'range',
                    label: 'Margin Left',
                    name: 'ml',
                    value: 0,
                    checked: false,
                  }, {
                    type: 'range',
                    label: 'Margin Top',
                    name: 'mt',
                    value: 0,
                    checked: false,
                  }, {
                    type: 'range',
                    label: 'Margin Right',
                    name: 'mr',
                    value: 0,
                    checked: false,
                  }, {
                    type: 'range',
                    label: 'Margin Bottom',
                    name: 'mb',
                    value: 0,
                    checked: false,
                  }
                ]
              }, {
                label: 'Border',
                toggled: false,
                onUpdate( section ) {
                  section.design.border   = this.fields.map( a => ( a.name + '-' + a.value ) ).join(' ');
                },
                fields: [
                  {
                    type: 'range',
                    label: 'Border Left',
                    name: 'border-l',
                    value: 0,
                    checked: false,
                  }, {
                    type: 'range',
                    label: 'Border Top',
                    name: 'border-t',
                    value: 0,
                    checked: false,
                  }, {
                    type: 'range',
                    label: 'Border Right',
                    name: 'border-r',
                    value: 0,
                    checked: false,
                  }, {
                    type: 'range',
                    label: 'Border Bottom',
                    name: 'border-b',
                    value: 0,
                    checked: false,
                  }
                ]
              }
            ]
          }
        ]
      })
    },
    removeSection( index ) {
      this.sections.splice( index, 1 );
    },
    focusSection( section ) {
      this.sections.forEach( p => p.focus = false );
      section.focus  = true;
      this.currentFocus   = section;
    },
    addRow( section ) {
      this.section.rows.push({
        layout: 'full',
        name: 'sample row',
        focus: false,
        components: []
      })
    },
    toggleSection( section, sections ) {
      const avoid   = sections.indexOf( section );

      sections.forEach( (s, _i) => {
        if ( _i !== avoid ) {
          s.toggled = false;
        }
      });

      section.toggled   = !section.toggled;
    }
  }
}
</script>
<template>
  <div id="pinso" class="flex h-full">
    <div id="pinso-sidebar" class="w-84 bg-gray-200 p-2">
      <div class="" v-if="currentFocus">
        <div class="title p-2 border-b-2 border-blue-400 flex justify-between">
          <h3 class="text-gray-800 text-2xl">
            <input type="text" v-model="currentFocus.name" class="bg-transparent outline-none w-full">
          </h3>
          <button class="rounded-full border border-gray-700 w-6 h-6 hover:shadow hover:border-transparent hover:bg-blue-400">*</button>
        </div>
        <div class="content my-2">
          <div class="tabs">
            <div class="tab-header flex justify-end">
              <div v-for="(tab, index) of currentFocus.tabs" v-bind:key="index" class="tab rounded-tr-lg rounded-tl-lg bg-white px-3 py-1 shadow relative">{{ tab.label }}</div>
            </div>
            <div class="tab-content bg-white p-2 shadow relative">
              <div v-for="(section, index) of currentFocusActiveTab.sections" :key="index">
                <h3 @click="toggleSection( section, currentFocusActiveTab.sections )" class="cursor-pointer font-semibold border-b-2 border-teal-400 py-2">{{ section.label }}</h3>
                <template v-if="section.toggled">
                  <div class="field py-2" v-for="(field, index) of section.fields" :key="index">
                    <label class="text-sm">{{ field.label }} ({{field.value}})</label>
                    <div class="flex" v-if="field.type === 'range'">
                      <input @change="section.onUpdate( currentFocus )" v-model="field.value" min="0" max="64" type="range" step="4" class="flex flex-auto">
                      <input type="checkbox" class="ml-2">
                    </div>
                  </div>
                </template>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div id="pinso-builder" class="flex flex-auto flex-col">
      <draggable v-model="sections" draggable=".pinso-section'" class="w-full">
          <div v-for="(section, index) of sections" class="pinso-section" @click="focusSection( section )" v-bind:key="index">
            <div 
              :class="section.focus ? 'border-blue-400' : 'border-gray-400'" 
              class="section-content border-2 page-section w-full p-2 my-2">
              <div :class="section.design.padding + ' ' + section.design.border + ' ' + section.design.margin" class="proper-section">
                <draggable v-model="section.rows" draggable=".pinso-row'" class="w-full">
                  
                </draggable>
                <div class="h-48 flex items-center justify-center w-full bg-blue-100" v-if="section.rows.length === 0">
                  <button @click="addRow( section )" class="rounded-full outline-none h-12 w-12 flex items-center justify-center bg-blue-400">+</button>
                </div>
              </div>
            </div>
          </div>
      </draggable>
      <div :class="sections.length === 0 ? 'h-48' : 'h-20'" class="flex items-center justify-center w-full bg-blue-100">
        <button @click="addSection()" class="rounded-full outline-none h-12 w-12 flex items-center justify-center bg-blue-400">+</button>
      </div>
    </div>
  </div>
</template>