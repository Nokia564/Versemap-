// @versemaps/plugin-heatmap/index.js
export default class HeatmapPlugin {
  static name = 'heatmap';
  static version = '1.0.0';

  constructor(map, options = {}) {
    this.map = map;
    this.options = {
      source: 'vm-data',
      intensity: 1,
      radius: 30,
     ...options
    };
    this.active = false;
  }

  async init() {
    // 1. Verifica se source existe
    if (!this.map.getSource(this.options.source)) {
      console.warn(`[HeatmapPlugin] Source ${this.options.source} não encontrado`);
      return;
    }

    // 2. Adiciona layer heatmap
    this.map.addLayer({
      id: 'heatmap-layer',
      type: 'heatmap',
      source: this.options.source,
      maxzoom: 15,
      paint: {
        'heatmap-intensity': this.options.intensity,
        'heatmap-radius': this.options.radius,
        'heatmap-color': [
          'interpolate',
          ['linear'],
          ['heatmap-density'],
          0, 'rgba(230,57,70,0)',
          0.2, '#E63946',
          0.4, '#F77F00',
          0.6, '#FCBF49',
          1, '#EAE2B7'
        ]
      },
      layout: { visibility: 'none' } // Começa desligado
    });

    this.active = true;
  }

  // API pública do plugin
  toggle() {
    const vis = this.map.getLayoutProperty('heatmap-layer', 'visibility');
    this.map.setLayoutProperty('heatmap-layer', 'visibility', vis === 'none'? 'visible' : 'none');
  }

  setIntensity(val) {
    this.map.setPaintProperty('heatmap-layer', 'heatmap-intensity', val);
  }

  destroy() {
    if (this.map.getLayer('heatmap-layer')) this.map.removeLayer('heatmap-layer');
    this.active = false;
  }
}
