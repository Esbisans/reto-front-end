<template>
<!-- tarjetas -->
  <div class="App">
    <div class="row">
      <div class="col-md-6 mb-3">
        <div class="card" style="width: 18rem;">
          <div class="card-header">
            <h2>Incompleta</h2>
          </div>
          <ul class="list-group list-group-flush">
            <li class="list-group-item" v-for="{id, title, due_date, is_completed} in incompletedTasks" :key="id">
              <div class="d-flex justify-content-around align-items-center">
                <button class="btn" style="color: #95190C" @click="deleteTask(id)"><i class="bi bi-trash-fill"></i></button>
                <div>
                  <h5>{{ title }}</h5>
                  <span>{{ due_date }}</span>
                </div>
                <button class="btn btn-link" style="color: #00171F;" @click="updateTask(id, title, due_date, is_completed=1)">
                  <i class="bi bi-caret-right-fill"></i>
                </button>
              </div>
            </li>

          </ul>
        </div>
      </div>

      <div class="col-md-6 mb-3">
        <div class="card" style="width: 18rem;">
          <div class="card-header">
            <h2>Completa</h2>
          </div>
          <ul class="list-group list-group-flush">
            <li class="list-group-item" v-for="{id, title, due_date, is_completed} in completedTasks" :key="id">
              <div class="d-flex justify-content-around align-items-center">
                <button class="btn" style="color: #00171F" @click="updateTask(id, title, due_date, is_completed = 0)"><i class="bi bi-caret-left-fill"></i></button>
                <div>
                  <h5>{{title}} </h5>
                  <span>{{due_date}}</span>
                </div>

                <button class="btn" style="color: #95190C" @click="deleteTask(id)"><i class="bi bi-trash-fill"></i></button>
              </div>
            </li>
          </ul>
        </div>
      </div>
      
    </div>


    <!-- modal add task-->

    <button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#addTaskModal">
      Agregar tarea
    </button>
    <div class="modal fade" id="addTaskModal" tabindex="-1" aria-labelledby="addTaskModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="addTaskModalLabel">Agregar tarea</h1>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <div class="input-group mb-3">
              <input v-model="titleRef" type="text" class="form-control" placeholder="Titulo" aria-label="title" aria-describedby="basic-addon1">
            </div>
            <div class="form-check d-flex justify-content-left mb-3" >
              <input v-model="isCompletedRef" class="form-check-input me-2" type="checkbox" value="" id="flexCheckDefault">
              <label class="form-check-label" for="flexCheckDefault">
                Completada
              </label>
            </div>
            <form class="mb-3">
              <div class="form-group">
                <input v-model="dueDateRef" type="date" class="form-control" id="fecha" name="fecha">
              </div>
            </form>
            <div class="input-group mb-3">
              <input v-model="commentsRef" type="text" class="form-control" placeholder="Comentarios" aria-label="comments" aria-describedby="basic-addon1">
            </div>
            <div class="input-group mb-3">
              <input v-model="descriptionRef" type="text" class="form-control" placeholder="Descripción" aria-label="description" aria-describedby="basic-addon1">
            </div>
            <div class="input-group mb-3">
              <input v-model="tagsRef" type="text" class="form-control" placeholder="Etiqueta" aria-label="tags" aria-describedby="basic-addon1">
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cerrar</button>
            <button @click="addTask" type="button" class="btn btn-primary" data-bs-dismiss="modal">Guardar tarea</button>
          </div>
        </div>
      </div>
    </div>


  </div>
</template>

<script>
import draggable from 'vuedraggable';
import axios from 'axios';
import { computed, onMounted, ref } from 'vue';

export default {


  setup() {

    const tasks = ref([]);

    //Metodo para obtener las tareas
    const getAllTask = async() =>{
    
      const paramsData = { token: 'hola' };
    
      try{
        const {data} = await axios.get(`https://ecsdevapi.nextline.mx/vdev/tasks-challenge/tasks`,
        {
          headers: {
            Authorization: `Bearer ${'e864a0c9eda63181d7d65bc73e61e3dc6b74ef9b82f7049f1fc7d9fc8f29706025bd271d1ee1822b15d654a84e1a0997b973a46f923cc9977b3fcbb064179ecd'}`
          },
          params: paramsData
        }
        )
    
        tasks.value = data;
        console.log(data);
      }catch (error) {
        //errorMessage.value = 'No se pudo cargar ese pokemon'
        console.log(error);
      }
    
    
    }

    //metodo para actualizar la tarea a la otra tarjeta
    const updateTask = async (taskId, title, due_date, is_completed) => {

      const updatedData = {
        token: 'hola',
        title: title,
        is_completed: is_completed,
        due_date: due_date,
        //comments: 'editar comentarios',
        //description: 'editar descripcion',
        //tags: 'editar tags',
      }

      try {
        const response = await axios.put(
          `https://ecsdevapi.nextline.mx/vdev/tasks-challenge/tasks/${taskId}`,
          updatedData,
          {
            headers: {
              Authorization: `Bearer ${'e864a0c9eda63181d7d65bc73e61e3dc6b74ef9b82f7049f1fc7d9fc8f29706025bd271d1ee1822b15d654a84e1a0997b973a46f923cc9977b3fcbb064179ecd'}`,
              'Content-Type': 'application/x-www-form-urlencoded'
            }
          }
        );

        // Actualizar la tarea en la lista local
        const updatedTaskIndex = tasks.value.findIndex(task => task.task_id === taskId);
        if (updatedTaskIndex !== -1) {
          tasks.value[updatedTaskIndex] = response.data;
        }
        getAllTask();
      } catch (error) {
        console.log(error);
      }
    }

    // Refs para los campos del modal
    const titleRef = ref('');
    const isCompletedRef = ref(false);
    const dueDateRef = ref('');
    const commentsRef = ref('');
    const descriptionRef = ref('');
    const tagsRef = ref('');


    //metodo para agregar una tarea
    const addTask = async () => {

      const title = titleRef.value;
      const isCompleted = isCompletedRef.value ? 1 : 0;
      const dueDate = dueDateRef.value;
      const comments = commentsRef.value;
      const description = descriptionRef.value;
      const tags = tagsRef.value;

      const dataTask = {

        token: 'hola',
        title: title,
        is_completed: isCompleted,
        due_date: dueDate,
        comments: comments,
        description: description,
        tags: tags,

      }

      try {
        const response = await axios.post(
          `https://ecsdevapi.nextline.mx/vdev/tasks-challenge/tasks`,
          dataTask,
          {
            headers: {
              Authorization: `Bearer ${'e864a0c9eda63181d7d65bc73e61e3dc6b74ef9b82f7049f1fc7d9fc8f29706025bd271d1ee1822b15d654a84e1a0997b973a46f923cc9977b3fcbb064179ecd'}`,
              'Content-Type': 'application/x-www-form-urlencoded'
            }
          }
        );

        getAllTask();
      } catch (error) {
        console.log(error);
      }
    }


    const deleteTask = async (taskId) => {

      const dataTask = { token: 'hola' };

      try {
        const response = await axios.delete(
          `https://ecsdevapi.nextline.mx/vdev/tasks-challenge/tasks/${taskId}`,
          {
            headers: {
              Authorization: `Bearer ${'e864a0c9eda63181d7d65bc73e61e3dc6b74ef9b82f7049f1fc7d9fc8f29706025bd271d1ee1822b15d654a84e1a0997b973a46f923cc9977b3fcbb064179ecd'}`
            },
            params: dataTask
          }
        );

        getAllTask();
      } catch (error) {
        console.log(error);
      }

    }

    onMounted(async () => {
      await getAllTask();
    });

    //dividr las tareas en completas e incompletas
    const completedTasks = computed(() => tasks.value.filter(task => task.is_completed === 1));
    const incompletedTasks = computed(() => tasks.value.filter(task => task.is_completed === 0));

    return {
      tasks,


      titleRef,
      isCompletedRef,
      dueDateRef,
      commentsRef,
      descriptionRef,
      tagsRef,

      getAllTask,
      completedTasks,
      incompletedTasks,
      updateTask,
      addTask,
      deleteTask
      
    }

  }

}


</script>


<style>
body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen',
    'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue',
    sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.App {
  height: 40vmin;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  text-align: center;
  align-items: center;
  justify-content: center;
  margin-top: 60px;
  color: #2c3e50;
  background-color: #00171f;
}
</style>
