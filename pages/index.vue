<template></template>

<script lang="ts" setup>
import { useMainStore } from '~/store/main'
import { useContext } from '@nuxtjs/composition-api'
import { onMounted } from '@nuxtjs/composition-api'
import { io } from 'socket.io-client'

enum NetworkPerformanceSpeed {
  low = '100kb',
  mid = '500kb',
  high = '2mb'
}
interface IUser {
  username: string
  speed: NetworkPerformanceSpeed
}

// User test //

// const main = useMainStore()
const { $axios } = useContext()
async function getAllUsers() {
  return await $axios.$get('http://localhost:3001/users')
}
async function getUser(id: string) {
  return await $axios.$get(`http://localhost:3001/users/${id}`)
}

async function createUser(params: IUser) {
  return await $axios.$post('http://localhost:3001/users', params)
}

async function updateUser(id: string, params: IUser) {
  return await $axios.$post(`http://localhost:3001/users/update/${id}`, params)
}

async function deleteUser(id: string) {
  return await $axios.$get(`http://localhost:3001/users/delete/${id}`)
}

const newUser = {
  username: 'nik',
  speed: NetworkPerformanceSpeed.high
}

const updatedUser = {
  username: 'nikolas',
  speed: NetworkPerformanceSpeed.low
}

async function testUsers() {
  // console.log(await createUser(newUser))
  const allUsersResponse = await getAllUsers()
  console.log(allUsersResponse)
  // const user0 = await getUser(allUsersResponse.data[0].userId)
  // console.log(user0)
  // const id = user0.data.userId
  // const editRes = await updateUser(id, updatedUser)
  // console.log(editRes)
  // deleteUser(id)
}

// chat test

async function createChat() {
  return await $axios.$get(`http://localhost:3001/chats/create`)
}
async function checkChatById(chatId: string) {
  return await $axios.$get(`http://localhost:3001/chats/check/${chatId}`)
}
async function getChatById(chatId: string) {
  return await $axios.$get(`http://localhost:3001/chats/${chatId}`)
}
async function registerUserByUserUUID(chatId: string, userId: string) {
  console.log(chatId, userId)
  return await $axios.$post(`http://localhost:3001/chats/register/${chatId}`, {
    userId
  })
}
async function postMessage(
  chatId: string,
  messageData: {
    userId: string
    username: string
    content: string
    date: Date
  }
) {
  return await $axios.$post(
    `http://localhost:3001/chats/${chatId}`,
    messageData
  )
}

async function testChats() {
  const chatId = 'wBkRoc0B'
  // createChat()

  await createUser(newUser)
  const allUsersResponse = await getAllUsers()
  const user0 = allUsersResponse.data[0]
  const user1 = allUsersResponse.data[1]
  console.log(allUsersResponse)
  // await checkChatById(chatId)
  await registerUserByUserUUID(chatId, user0.userId)
  await registerUserByUserUUID(chatId, user1.userId)
  // const chatData = await getChatById(chatId)
  // postMessage(chatId, {
  //   username: user0.username,
  //   userId: user0.userId,
  //   date: new Date(),
  //   content: 'hello world!'
  // })
  // postMessage(chatId, {
  //   username: user1.username,
  //   userId: user1.userId,
  //   date: new Date(),
  //   content: 'abra cadabra'
  // })
  // postMessage(chatId, {
  //   username: user0.username,
  //   userId: user0.userId,
  //   date: new Date(),
  //   content: 'hello world answer!'
  // })
  setTimeout(async () => {
    const chatData = await getChatById(chatId)
    const content = JSON.parse(chatData.data.chatData.content)
    content.forEach((message: string) => {
      const parsed = JSON.parse(message)
      console.log(parsed)
    })
  }, 2000)
}

function handleSocketConnection() {
  const socket = new WebSocket('ws://localhost:3001')
  socket.onopen = () => {
    socket.send(
      JSON.stringify({
        event: 'join',
        data: {
          dsfdsf: 'fdfdfd'
        }
      })
    )
  }
}

onMounted(async () => {
  // testUsers()
  // testChats()
  handleSocketConnection()
})
</script>
