<template>
  <div class="tree">
    <svg
      ref="svg"
      :width="viewer.w"
      :height="viewer.w"
      style="touch-action: none; -webkit-tap-highlight-color: rgba(0, 0, 0, 0);"
    >
      <g :transform="`translate(${zoom.x},${zoom.y})scale(${zoom.k})`">
        <path
          class="link"
          v-for="(line,index) in lines"
          :d="getDiagonal(line)"
          stroke="#313642"
          stroke-width="1"
          fill="none"
          v-bind:key="index"
        />
        <g
          class="node"
          v-for="node in nodes"
          :transform="`translate(${node.x},${node.y})`"
          v-bind:key="node.id"
        >
          <rect fill="#313642" stroke="#313642" stroke-width="1" width="180" height="180" />
          <text fill="#fff" x="90" y="22" style="font-size:15px;" text-anchor="middle">방향</text>
          <image href="@/assets/nations/82.png" height="30" width="30" x="145" y="0" />

          <text fill="#fff" x="90" y="44" style="font-size:15px;" text-anchor="middle">이름</text>
          <text fill="#fff" x="90" y="66" style="font-size:15px;" text-anchor="middle">등급</text>
          <text
            fill="#fff"
            x="90"
            y="88"
            style="font-size:15px;"
            text-anchor="middle"
          >{{node.data.name}}</text>
          <text fill="#fff" x="90" y="110" style="font-size:15px;" text-anchor="middle">2020-08-12</text>
          <text
            fill="#87CEEB"
            x="90"
            y="132"
            style="font-size:15px;"
            text-anchor="middle"
          >정기구매가입/개인유지</text>
        </g>
      </g>
    </svg>
  </div>
</template>

<script>
import * as d3 from "d3";
export default {
  name: "tree",
  data() {
    return {
      viewer: {
        w: 600,
        h: 600,
      },
      a: [],
      nodes: [],
      lines: [],
      zoom: {
        x: 140,
        y: 0,
        k: 0.2,
      },
      data: [
        {
          id: "cryu",
          countryCode: null,
          pid: null,
        },
        {
          id: "mai",
          countryCode: "82",
          pid: "cryu",
        },
        {
          id: "cherin",
          countryCode: "82",
          pid: "mai",
        },
        {
          id: "takasaki",
          countryCode: "82",
          pid: "cryu",
        },
        {
          id: "otoko",
          countryCode: "82",
          pid: "mai",
        },
        {
          id: "sam",
          countryCode: "82",
          pid: "cherin",
        },
      ],
    };
  },
  created() {
    // this.setData()
    this.viewer.w = window.innerWidth;
  },
  async mounted() {
    await this.d3Set();
    await this.setZoom();
  },
  methods: {
    d3Set() {
      const stratify = d3
        .stratify()
        .id((d) => d.id)
        .parentId((d) => d.pid);
      // const tree = d3.tree().size([2000, 2000]);
      const tree = d3.tree().nodeSize([250, 340]);

      const rootNode = stratify(this.data);
      const treeData = tree(rootNode);

      this.nodes = rootNode.descendants();

      this.lines = this.nodes.slice(1);
    },
    onZoom() {
      this.zoom.x = d3.event.transform.x;
      this.zoom.y = d3.event.transform.y;
      this.zoom.k = d3.event.transform.k;
    },
    setZoom() {
      const zoom = d3.zoom().scaleExtent([0.2, 5]).on("zoom", this.onZoom);
      const selection = d3.select(this.$refs.svg);
      selection.call(zoom).call(zoom.transform, this.initZoom());
    },
    initZoom() {
      this.zoom.x = 140;
      this.zoom.y = 0;
      this.zoom.k = 0.2;
      return d3.zoomIdentity
        .translate(this.zoom.x, this.zoom.y)
        .scale(this.zoom.k);
    },
    getDiagonal(d) {
      // return `M${d.x+75},${d.y} C${d.x+75},${d.y} ${d.parent.x+75},${d.parent.y+50} ${d.parent.x+75}, ${d.parent.y + 50}`
      return `M${d.x + 95},${d.y} L${d.x + 95},${
        (d.parent.y + d.y) / 2 + 100
      } L${d.parent.x + 95},${(d.parent.y + d.y) / 2 + 100} L${
        d.parent.x + 95
      }, ${d.parent.y + 50} `;
    },
  },
};
</script>

<style>
</style>