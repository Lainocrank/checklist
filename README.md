<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Checklist con Subtareas</title>
  <style>
    body{margin:0;font-family:sans-serif;background:#0f1724;color:#e6eef8;}
    .wrap{max-width:800px;margin:20px auto;padding:20px}
    h1{margin:0 0 10px}
    .card{background:#0b1220;border-radius:12px;padding:18px;margin-bottom:16px;box-shadow:0 6px 18px rgba(2,6,23,0.6)}
    .btn{background:#1e293b;border:none;color:#e6eef8;padding:6px 12px;border-radius:6px;cursor:pointer;font-size:13px}
    .btn.small{padding:3px 8px;font-size:12px}
    .btn.strong{background:linear-gradient(90deg,#7c3aed,#5b21b6)}
    .task,.subtask{display:flex;align-items:center;gap:8px;margin-bottom:8px}
    .task input[type=text],.subtask input[type=text]{flex:1;padding:6px;border-radius:6px;border:1px solid #334155;background:#0f172a;color:inherit}
    .subtasks{margin-left:24px}
    .done{text-decoration:line-through;opacity:0.6}
    #progressBarWrap{margin:10px 0;}
    #progressBar{height:10px;border-radius:8px;background:linear-gradient(90deg,#7c3aed,#5b21b6);width:0%;transition:width 0.3s ease;}
    .small{font-size:12px;color:#94a3b8}
  </style>
</head>
<body>
  <div class="wrap">
    <h1>Checklist con subtareas</h1>

    <div id="progressBarWrap">
      <div id="progressBar"></div>
    </div>
    <p id="progressText" class="small">0% completado</p>

    <form id="addTaskForm" onsubmit="return false;">
      <input id="newTaskInput" type="text" placeholder="Añadir nueva tarea" />
      <button class="btn strong">Añadir tarea</button>
    </form>

    <div id="taskList"></div>
  </div>

<script>
  const taskList = document.getElementById("taskList");
  const newTaskInput = document.getElementById("newTaskInput");
  const addTaskForm = document.getElementById("addTaskForm");

  let tasks = [
    { text: "Preparar desayuno", done:false, subtasks:[{text:"Tostar pan",done:false},{text:"Hervir café",done:false}]},
    { text: "Hacer ejercicio", done:false, subtasks:[{text:"Calentamiento",done:false}]},
    { text: "Revisar correos", done:false, subtasks:[]},
    { text: "Estudiar 1 hora", done:false, subtasks:[{text:"Leer apuntes",done:false}]},
    { text: "Llamar a un amigo", done:false, subtasks:[]}
  ];

  function render(){
    taskList.innerHTML = "";
    tasks.forEach((task,idx)=>{
      const div = document.createElement("div");
      div.className="card";

      const taskRow = document.createElement("div");
      taskRow.className="task";

      const cb = document.createElement("input");
      cb.type="checkbox"; cb.checked=task.done;
      cb.onchange=()=>{ task.done=cb.checked; updateProgress(); render(); };

      const txt = document.createElement("input");
      txt.type="text"; txt.value=task.text;
      if(task.done) txt.classList.add("done");
      txt.onchange=()=>{ task.text=txt.value; };

      const delBtn = document.createElement("button");
      delBtn.className="btn small"; delBtn.textContent="🗑";
      delBtn.onclick=()=>{ tasks.splice(idx,1); updateProgress(); render(); };

      const addSubBtn = document.createElement("button");
      addSubBtn.className="btn small"; addSubBtn.textContent="➕ Subtarea";
      addSubBtn.onclick=()=>{
        if(task.subtasks.length<10){
          task.subtasks.push({text:"Nueva subtarea",done:false});
          render();
        }else{
          alert("Máximo 10 subtareas por tarea");
        }
      };

      taskRow.appendChild(cb);
      taskRow.appendChild(txt);
      taskRow.appendChild(addSubBtn);
      taskRow.appendChild(delBtn);
      div.appendChild(taskRow);

      // Subtareas
      const subsDiv = document.createElement("div");
      subsDiv.className="subtasks";
      task.subtasks.forEach((sub,sidx)=>{
        const subRow=document.createElement("div"); subRow.className="subtask";
        const scb=document.createElement("input"); scb.type="checkbox"; scb.checked=sub.done;
        scb.onchange=()=>{ sub.done=scb.checked; updateProgress(); render(); };
        const stxt=document.createElement("input"); stxt.type="text"; stxt.value=sub.text;
        if(sub.done) stxt.classList.add("done");
        stxt.onchange=()=>{ sub.text=stxt.value; };
        const sdel=document.createElement("button"); sdel.className="btn small"; sdel.textContent="🗑";
        sdel.onclick=()=>{ task.subtasks.splice(sidx,1); updateProgress(); render(); };
        subRow.appendChild(scb); subRow.appendChild(stxt); subRow.appendChild(sdel);
        subsDiv.appendChild(subRow);
      });
      div.appendChild(subsDiv);

      taskList.appendChild(div);
    });
    updateProgress();
  }

  function updateProgress(){
    let total=0, done=0;
    tasks.forEach(t=>{
      total++; if(t.done) done++;
      t.subtasks.forEach(s=>{ total++; if(s.done) done++; });
    });
    const percent = total? Math.round(done/total*100):0;
    document.getElementById("progressBar").style.width=percent+"%";
    document.getElementById("progressText").textContent=`${done} de ${total} elementos (${percent}%)`;
  }

  addTaskForm.addEventListener("submit",()=>{
    const v=newTaskInput.value.trim();
    if(v){
      tasks.push({text:v,done:false,subtasks:[]});
      newTaskInput.value="";
      render();
    }
  });

  render();
</script>
</body>
</html>
