//importa React y useState desde el módulo 'react'. useState es un hook que permite agregar estado a componentes de React.
import React, {useState} from 'react';
//importa componentes específicos de React Native que se utilizarán en la aplicación.
import { KeyboardAvoidingView, StyleSheet, Text, View, TextInput, TouchableOpacity, Keyboard, ScrollView } from 'react-native';
//importa un componente personalizado llamado Task desde un archivo Task.js ubicado en la carpeta /components.
import Task from './components/Task';
import * as Notification from 'expo-notifications';
//define el componente App, que es el componente principal de la aplicación.
export default function App() {
  //declara un estado llamado task y una función para actualizar ese estado llamada setTask. task se inicializa en undefined.
  const [task, setTask] = useState();
  //declara un estado llamado description y una función para actualizar ese estado llamada setDescription. description se inicializa en undefined.
  const [description, setDescription] = useState();
  //declara un estado llamado taskItems y una función para actualizar ese estado llamada setTaskItems. taskItems se inicializa en una matriz vacía.
  const [taskItems, setTaskItems] = useState([]);
//define una función handleAddTask que se ejecuta cuando se presiona el botón de agregar tarea. La función agrega un objeto {task, description} al estado taskItems, limpia los estados de task y description y oculta el teclado.
  const handleAddTask = () => {
    //aca va la notificacion:
    // esta notificacion se activa a los 5 minutos de agregada una tarea, por eso
    //se activa al pulsar el boton + el cual es el encargado de agregar tareas

    Notification.scheduleNotificationAsync({
      content:{
        title:"App Tareas",
        body:"Queda poco tiempo para finalizar la tarea",
      },
      trigger:{
        seconds: 300,
      },
    });

    Keyboard.dismiss();
    setTaskItems([...taskItems, {task: task, description: description}])
    setTask(null);
    setDescription(null);
  }
//define una función completeTask que se ejecuta cuando se completa una tarea. La función crea una copia del estado taskItems, elimina el elemento del índice dado y actualiza el estado taskItems con la copia modificada.
  const completeTask = (index) => {
    let itemsCopy = [...taskItems];
    itemsCopy.splice(index, 1);
    setTaskItems(itemsCopy)
  }
//define una función showTaskDetail que muestra una alerta con la tarea y su descripción cuando se presiona el botón de detalles.
  const showTaskDetail = (index) => {
    alert(`Tarea: ${taskItems[index].task}\nDescripción: ${taskItems[index].description}`);
  }
//define el JSX que se renderizará en la pantalla. Este incluye un ScrollView con un título, una lista de tareas y un formulario para agregar tareas. También incluye un TouchableOpacity con un botón '+' para agregar tareas.
  return (
    <View style={styles.container}>
      <ScrollView
        contentContainerStyle={{
          flexGrow: 1
        }}
        keyboardShouldPersistTaps='handled'
      >
        <View style={styles.tasksWrapper}>
          <Text style={styles.sectionTitle}>Tareas para hoy!</Text>
          <View style={styles.items}>
            {
              taskItems.map((item, index) => {
                return (
                  <TouchableOpacity key={index} onPress={() => completeTask(index)}>
                    <View style={styles.item}>
                      <Task text={item.task} description={item.description} /> 
                      <TouchableOpacity onPress={() => showTaskDetail(index)}>
                        <View style={styles.detailButton}>
                          <Text style={styles.detailButtonText}>Detalle</Text>
                        </View>
                      </TouchableOpacity>
                    </View>
                  </TouchableOpacity>
                )
              })
            }
          </View>
        </View>
      </ScrollView>
      <View style={styles.writeTaskWrapper}>
        <TextInput style={styles.input} placeholder={'Escribe una tarea'} value={task} onChangeText={text => setTask(text)} />
        <TextInput style={styles.input} placeholder={'Escribe una descripción'} value={description} onChangeText={text => setDescription(text)} />
        <TouchableOpacity onPress={() => handleAddTask()}>
          <View style={styles.addWrapper}>
            <Text style={styles.addText}>+</Text>
          </View>
        </TouchableOpacity>
      </View>
    </View>
  );
}
//define los estilos para los componentes. Esto incluye la apariencia de los títulos, las tareas, el formulario y el botón de agregar tarea.
const styles = StyleSheet.create({
  //contenedor
  container: {
    flex: 1,
    backgroundColor: '#E8EAED',
  },
//contenedor de el titulo y las tareas
  tasksWrapper: {
    paddingTop: 80,
    paddingHorizontal: 20,
  },
  //titulo
  sectionTitle: {
    fontSize: 24,
    fontWeight: 'bold'
  },
  //contenedor de cada tarea
  items: {
    marginTop: 20,
  },
  //tareas
  item: {

    flexDirection: 'row',
    justifyContent: 'space-between',
    marginBottom: 20,

  },
  //contenedor de los input y el boton +
  writeTaskWrapper: {
    
    position: 'absolute',
    bottom: 60,
    width: '100%',
    flexDirection: 'row',
    justifyContent: 'space-around',
    alignItems: 'center'
  },
  //inputs
  input: {
    paddingVertical: 15,
    paddingHorizontal: 15,
    backgroundColor: '#FFF',
    borderRadius: 60,
    borderColor: '#C0C0C0',
    borderWidth: 1,
    width: 140,
    height:70,
  },
  //boton +
  addWrapper: {
    width: 60,
    height: 60,
    backgroundColor: '#FFF',
    borderRadius: 60,
    justifyContent: 'center',
    alignItems: 'center',
    borderColor: '#C0C0C0',
    borderWidth: 1,
  },
  //boton que muestra el detalle de las tareas
  detailButton: {
    width: 60,
    height: 48,
    backgroundColor: '#FFF',
    borderRadius: 60,
    justifyContent: 'center',
    alignItems: 'center',
    borderColor: '#C0C0C0',
    borderWidth: 1,
  },
});
