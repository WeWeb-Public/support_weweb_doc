<!-- This is a Vue.js single file component. -->
<!-- Check the Vue.js doc here :  -->
<!-- https://vuejs.org/v2/guide/ -->

<!-- This is your HTML -->
<template>
    <div class="slack-support">
        <!-- wwManager:start -->
        <wwSectionEditMenu v-bind:sectionCtrl="sectionCtrl"></wwSectionEditMenu>
        <!-- wwManager:end -->
        <div class="elem-container" v-for="(elem, index) in section.data.elems" :key="elem.id">
            <div class="elem">
                <div class="img">
                    <svg class="svg" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" viewBox="0 0 1132 1211">
                        <defs>
                            <svg:style type="text/css">.c{fill:white;}</svg:style>
                            <linearGradient :id="elem.id" x1="0" x2="100%" y1="0%" y2="0" gradientUnits="objectBoundingBox">
                                <stop v-for="(color) in elem.gradient.colors" :key="color" :stop-color="color.col" :offset="`${color.pos}%`"/>
                            </linearGradient>
                        </defs>
                        <g id="b" class="a">
                            <path v-if="!(index % 2)"  :style="{ fill: `url(#${elem.id})` }" class="b" d="M649.894,257.468c295.57-133.029,851.876,383.081,754.9,697.8S436.291,1519.823,293.635,1388.027,354.325,390.5,649.894,257.468Z" transform="translate(366.8 1814.671) rotate(-137)"></path>
                            <path v-else :style="{ fill: `url(#${elem.id})` }" class="b" d="M649.894,257.468c295.57-133.029,851.876,383.081,754.9,697.8S436.291,1519.823,293.635,1388.027,354.325,390.5,649.894,257.468Z" transform="matrix(0.643, 0.766, -0.766, 0.643, 902.072, -505.91)"/>
                        </g>
                    </svg>
                </div>
                <!-- wwManager:start -->
                <wwContextMenu class="ww-orange-button svg" :class="{ right: !(index % 2) }" tag="div" :options="elemOptions" @color="elemColor(index)" @remove="remove(section.data.elems, { index })" @addBefore="elemAdd(index)" @addAfter="elemAdd(index+1)" v-if="sectionCtrl.getEditMode() == 'CONTENT'">
                    <wwOrangeButton></wwOrangeButton>
                </wwContextMenu>
                <!-- wwManager:end -->
                <div class="content-center">
                    <div class="content">
                        <wwLayoutColumn tag="div" ww-default="ww-text" :ww-list="elem.list" class="list" @ww-add="add(elem.list, $event)" @ww-remove="remove(elem.list, $event)">
                            <wwObject tag="div" v-for="object in elem.list" :key="object.uniqueId" :ww-object="object"></wwObject>
                        </wwLayoutColumn>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<!-- This is your Javascript -->
<!-- ✨ Here comes the magic ✨ -->
<script>
export default {
    name: "__COMPONENT_NAME__",
    props: {
        // The section controller object is passed. You can access it anytime
        sectionCtrl: Object
    },
    data() {
        return {
            /* wwManager:start */
            elemOptions: {
                items: [
                    {
                        text: {
                            en: 'Color',
                            fr: 'Couleur'
                        },
                        icon: 'wwi wwi-color',
                        action: 'color'
                    },
                    {
                        text: {
                            en: 'Before',
                            fr: 'Avant'
                        },
                        icon: 'wwi wwi-add',
                        action: 'addBefore'
                    },
                    {
                        text: {
                            en: 'After',
                            fr: 'Apres'
                        },
                        icon: 'wwi wwi-add',
                        action: 'addAfter'
                    },
                    {
                        text: {
                            en: 'Delete',
                            fr: 'Supprimer'
                        },
                        icon: 'wwi wwi-delete',
                        action: 'remove'
                    }
                ]
            }
            /* wwManager:end */
        }
    },
    computed: {
        // The section object contains all the info and data about the section
        // Use it as you like !
        section() {
            return this.sectionCtrl.get();
        }
    },
    created() {
        // Initialize the data once the section is created in the DOM
        this.init()
    },
    methods: {
        init() {
            // Initialize section data
            let needUpdate = false

            // We will only save the data in this.section.data
            // So you need to put in there all the data of you WeWeb objects
            this.section.data = this.section.data || {};

            // Initialize WeWeb objects that are in the html template up there
            if (!this.section.data.background) {
                this.section.data.background = wwLib.wwObject.getDefault({
                    type: 'ww-color'
                });
                needUpdate = true
            }

            if (!this.section.data.elems) {
                this.section.data.elems = []
                this.elemAdd(0)
                this.elemAdd(0)
                needUpdate = true
            }

            if (!this.section.data.contentList) {
                this.section.data.contentList = [];
                needUpdate = true;
            }

            if (needUpdate) {
                this.sectionCtrl.update(this.section);
            }
        },

        // --------- EDITOR FUNCTIONS ---------
        // All the codes between /* wwManager:start */ and /* wwManager:end */ are only for editor purposes
        // So It won't in the published website!
        /* wwManager:start */
        getGradientObj(wwObjectGradient) {
            if (!wwObjectGradient) return {}
            let gradient = {}
            let regDeg = /linear-gradient\(([0-9]+)deg/;
            let matchesDeg = wwObjectGradient.match(regDeg);
            if (matchesDeg.length >= 2) {
                gradient.angle = matchesDeg[1];
            }
            let regColors = /(?:#([0-9A-F]+)\s([0-9]+)+%)/gi;
            let matchesColors = wwObjectGradient.match(regColors);
            gradient.colors = [];
            for (let match of matchesColors) {
                let color = {};
                let regColor = /(#[A-F0-9]+)/i
                let matchesColor = match.match(regColor);
                if (matchesColor.length >= 2) {
                    color.col = matchesColor[1];
                }
                let regPos = /\s([0-9]+)%/
                let matchesPos = match.match(regPos);
                if (matchesPos.length >= 2) {
                    color.pos = matchesPos[1];
                }
                gradient.colors.push(color);
            }
            gradient.defaultColor = gradient.colors.length ? gradient.colors[0].col : '#FFFFFF';
            this.gradient = gradient;
            return gradient
        },
        // getGradientCenter(gradient) {
        //     const pi = (gradient.angle) * (Math.PI / 180);
        //     // console.log(137 + 90 - gradient.angle)
        //     console.log(Math.round(50 + Math.sin(pi) * 50) + '%')
        //     return {
        //         x1: Math.round(50 + Math.sin(pi) * 50) + '%',
        //         y1: Math.round(50 + Math.cos(pi) * 50) + '%',
        //         x2: Math.round(50 + Math.sin(pi + Math.PI) * 50) + '%',
        //         y2: Math.round(50 + Math.cos(pi + Math.PI) * 50) + '%',
        //     }
        // },
        async elemColor(index) {
            let options = {
                firstPage: 'GRADIENT',
                data: {
                    gradientColor: this.section.data.elems[index].color,
                    gradient: this.section.data.elems[index].gradient
                }
            }
            try {
                const result = await wwLib.wwPopups.open(options)
                if (typeof(result) !== 'undefined') {
                    this.section.data.elems[index].color = result.gradientColor
                    this.section.data.elems[index].gradient = this.getGradientObj(result.gradient)
                }
                this.sectionCtrl.update(this.section)
            } catch (err) {
                wwLib.wwLog.error(err)
            }
        },
        elemAdd(index) {
            try {
                const data = {
                    id: wwLib.wwUtils.getUniqueId(),
                    img: wwLib.wwObject.getDefault({ type: 'ww-image' }),
                    gradient: {},
                    list: []
                }
                this.section.data.elems.splice(index, 0, data);
                this.sectionCtrl.update(this.section);
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        },
        add(list, options) {
            try {
                list.splice(options.index, 0, options.wwObject);
                this.sectionCtrl.update(this.section);
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        },
        remove(list, options) {
            try {
                list.splice(options.index, 1);
                this.sectionCtrl.update(this.section);
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        }
        /* wwManager:end */
    }
};
</script>

<!-- This is your CSS -->
<!-- Add "scoped" attribute to limit CSS to this component only -->
<!-- Add lang="scss" or others to use computed CSS -->
<style lang="scss" scoped>
.slack-support {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -ms-flex-wrap: wrap;
  flex-wrap: wrap;
  .elem-container {
    position: relative;
    width: 100%;
    @media (min-width: 1024px) {
      width: 50%;
      &:nth-child(2n + 1) {
        margin-top: 25vh;
      }
    }
    .elem {
      position: relative;
      .img {
        position: relative;
        pointer-events: all;
        .svg {
          position: relative;
        }
        .obj {
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
        }
      }
      .content-center {
        position: absolute;
        top: 0%;
        left: 0;
        width: 100%;
        height: 100%;
        display: -webkit-box;
        display: -ms-flexbox;
        display: flex;
        -webkit-box-align: center;
        -ms-flex-align: center;
        align-items: center;
        .content {
          width: 100%;
        }
      }
    }
  }
}
/* wwManager:start */
.slack-support {
  .ww-orange-button.svg {
    position: absolute;
    top: 50%;
    left: 20px;
    -webkit-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
    z-index: 2;
    &.right {
      left: unset;
      right: 0;
    }
  }
}
/* wwManager:end */
</style>
