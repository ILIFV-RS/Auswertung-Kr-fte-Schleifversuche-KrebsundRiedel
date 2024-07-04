<!--
author:   Ronny Stolze

email:    ronny.stolze@h2.de

version:  0.0.1

language: de

narrator: DE German Female

comment:  Auswertung der Schleifversuche Krebs&Riedel vom 26.-28.06.2024

script:  https://cdnjs.cloudflare.com/ajax/libs/echarts/4.2.1/echarts-en.js
-->


## Kraftmessung Grundlagenversuche Flachschleifen

**Laden der Textdatei mit den Messdaten der Kraftmessung!**

Nach der Auswahl einer Textdatei wird automatisch das Diagramm erstellt.

<input type="file" onchange="getFileContent(this.files)">

<script>
window.getFileContent = (files) => {
	if (files.length !== 1) {
  	// Sicherstellen, dass nur eine Datei hochgeladen wurde.
    return;
  }
  const reader = new FileReader();
  reader.addEventListener("loadend", () => {
  	// in reader.result stehen die bytes
    // also müssen wir es noch in text umwandeln.

    const decoder = new TextDecoder();
    const textValue = decoder.decode(reader.result);

    const data = textValue.replace(/,/g, ".");

    const split = data.match(/\d+(?:\.\d+)?|\-\d+(?:\.\d+)?/g);
    const T = []
    const Fn_Spindel = []
    const Fn_Tisch = []
    const Ft_Spindel = []
    const Ft_Tisch = []

    for(let i=0; i<split.length; i=i+5) {
      T.push(parseFloat(split[i]));
      Fn_Spindel.push(parseFloat(split[i+1]));
      Fn_Tisch.push(parseFloat(split[i+2]));
      Ft_Spindel.push(parseFloat(split[i+3]));
      Ft_Tisch.push(parseFloat(split[i+4]));
    }

    plotData(T, Fn_Spindel, Fn_Tisch, Ft_Spindel, Ft_Tisch);

    // Jetzt kannst du dinge mit dem text machen
  	document.getElementById("content").innerText = textValue;
  });
  reader.readAsArrayBuffer(files[0]);
}

function plotData(t, y1, y2, y3, y4) {

  let main = document.getElementById('main');
  main.hidden = false;

  let fy1 = []
  let fy2 = []
  let fy3 = []
  let fy4 = []

  for(let i=0; i<t.length; i++) {
    fy1.push([t[i], y1[i]])
    fy2.push([t[i], y2[i]])
    fy3.push([t[i], y3[i]])
    fy4.push([t[i], y4[i]])
  }

  let chart = echarts.init(main);

  let option = {

    title : {
      display: false,
      text: "",
      subtext: '',
      itemGap: 10,
      textAlign: 'auto',
      textVerticalAlign: 'middle',
      textStyle: {
        fontSize: 30,
      },
      subtextStyle: {
        fontSize: 20,
      },
    },

    grid: {
      top: 120,
    },

    legend: {
        data:['Fn_Spindel', 'Fn_Tisch', 'Ft_Spindel', 'Ft_Tisch'],
        top: 80,
        itemGap: 30,
        itemWidth: 50,
        itemHeight: 20,
        textStyle: {
          fontSize: 24,
        },
    },

    toolbox: {
      show : true,
      feature : {
        mark : {show: true},
        dataZoom : {show: true},
        dataView : {show: true, readOnly: false},
        restore : {show: true},
        saveAsImage : {
          show: true,
          pixelRatio: 4,
        },
      },
    },

    xAxis: [{
      type: 'value',
      name: 'Zeit in s',
      nameLocation: 'middle',
      nameGap: 40,
      axisLabel: {
        fontSize: 20,
      },
      nameTextStyle: {
        fontSize: 20,
      },
    }],

    yAxis: [{
      type : 'value',
      name: 'Kraft in N',
      nameLocation: 'middle',
      nameGap: 60,
      axisLabel: {
        fontSize: 20,
      },
      nameTextStyle: {
        fontSize: 20,
      },
    }],


    series : [{
      name:'Fn_Spindel',
      type:'line',
      data: fy1,
      symbol: 'none',
      lineStyle: {
        width: 3,
      },
    },
    {
      name:'Fn_Tisch',
      type:'line',
      data: fy2,
      symbol: 'none',
      lineStyle: {
        width: 3,
      },
    },
    {
      name:'Ft_Spindel',
      type:'line',
      data: fy3,
      symbol: 'none',
      lineStyle: {
        width: 3,
      },
    },
    {
      name:'Ft_Tisch',
      type:'line',
      data: fy4,
      symbol: 'none',
      lineStyle: {
        width: 3,
      },
    }]
  };

  // use configuration item and data specified to show chart
  chart.setOption(option);

  window.addEventListener('resize', chart.resize);
}
</script>


<div id="main" style="position:relative; width:100%; height:100%;" hidden="true"></div>
