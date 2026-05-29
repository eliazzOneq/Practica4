<script setup>
  import { ref, onMounted } from 'vue'

  import {
    getProductos,
    createProducto,
    updateProducto,
    deleteProducto
  } from '@/services/productoService'

  const productos = ref([])
  const editando = ref(false)

  const form = ref({
    id:null,
    nombre:'',
    descripcion:'',
    precio:'',
    stock:''
  })

  const cargarProductos = async()=>{
    const response = await getProductos()
    productos.value = response.data
  }

  const guardarProducto = async()=>{
    try{
      if(editando.value){
        await updateProducto(
          form.value.id,
          form.value
        )
      } else { await createProducto(form.value) }
      limpiarFormulario()
      await cargarProductos()
    } catch(error){
      alert('Ocurrió un error.')
      console.error(error)
    }
    alert('Producto guardado correctamente')
  }

  const editarProducto=(producto)=>{
    editando.value=true
    form.value={...producto}
  }

  const limpiarFormulario=()=>{
    editando.value=false
    form.value={
      id:null,
      nombre:'',
      descripcion:'',
      precio:'',
      stock:''
    }
  }

  const eliminarProducto=async(id)=>{
    if(confirm('¿Eliminar producto?')){
      await deleteProducto(id)
      await cargarProductos()
    }
    alert('Producto eliminado')
  }

  onMounted(()=>{
    cargarProductos()
  })
</script>

<template>
  <h1>CRUD Productos</h1>
  <h2>
    {{ editando
    ? 'Editar Producto'
    : 'Nuevo Producto'
    }}
  </h2>

  <input v-model="form.nombre" placeholder="Nombre"/>
  <input v-model="form.descripcion" placeholder="Descripción"/>
  <input v-model="form.precio" type="number" placeholder="Precio"/>
  <input v-model="form.stock" type="number" placeholder="Stock"/>

  <button @click="guardarProducto">
    {{ editando
    ? 'Actualizar'
    : 'Guardar'
    }}
  </button>

  <button v-if="editando" @click="limpiarFormulario">Cancelar</button>
  <hr>
  <ul>
    <li v-for="producto in productos" :key="producto.id">
      {{ producto.nombre }} - ${{ producto.precio }} - Stock: {{ producto.stock }}

      <button @click="editarProducto(producto)">Editar</button>

      <button @click="eliminarProducto(producto.id)">Eliminar</button>
    </li>
  </ul>
</template>