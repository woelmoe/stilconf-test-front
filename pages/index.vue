<template></template>

<script lang="ts" setup>
// import { useMainStore } from '~/store/main'
import { useContext } from '@nuxtjs/composition-api'
import { onMounted } from '@nuxtjs/composition-api'

enum NetworkPerformanceSpeed {
  low = '100kb',
  mid = '500kb',
  high = '2mb'
}
interface IUser {
  username: string
  speed: NetworkPerformanceSpeed
}

function delay(ms: number) {
  return new Promise((resolve) => setTimeout(resolve, ms))
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
async function registerUserByUserUUID(
  chatId: string,
  userId: string,
  username: string
) {
  console.log(chatId, userId, username)
  return await $axios.$post(`http://localhost:3001/chats/register/${chatId}`, {
    userId,
    username
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
  const chatId = 'T2h0ljI5'
  // createChat()

  await createUser(newUser)
  const allUsersResponse = await getAllUsers()
  console.log(allUsersResponse)
  const user0 = allUsersResponse.data[0]
  const user = allUsersResponse.data[1]
  console.log(allUsersResponse)
  // await checkChatById(chatId)
  await registerUserByUserUUID(chatId, user0.userId, user0.username)
  await registerUserByUserUUID(chatId, user.userId, user.username)
  const chatData = await getChatById(chatId)
  postMessage(chatId, {
    username: user0.username,
    userId: user0.userId,
    date: new Date(),
    content: 'hello world!'
  })
  // postMessage(chatId, {
  //   username: user.username,
  //   userId: user.userId,
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

async function handleSocketConnection() {
  // const chat = await createChat()
  // const chatId = chat.data.chatId
  // console.log(chat)

  const chatId = 'GM2wCEOK'
  const socket = new WebSocket('ws://localhost:3001')

  socket.onopen = async () => {
    const user = await createUser(newUser)
    await delay(2000)

    // const user1 = await createUser(newUser)
    // console.log(user)
    // console.log(user1)
    // await delay(2000)

    socket.send(
      JSON.stringify({
        event: 'Join',
        data: {
          userId: user.data.userId,
          bitrate: user.data.speed,
          username: user.data.username,
          // roomId: chat.data.chatId
          roomId: chatId
        }
      })
    )
    await delay(2000)

    //   socket.send(
    //     JSON.stringify({
    //       event: 'BroadcastMessage',
    //       data: {
    //         username: user.username,
    //         userId: user.userId,
    //         date: new Date(),
    //         content: 'hello world!',
    //         chatId: chatId
    //       }
    //     })
    //   )

    // socket.send(
    //   JSON.stringify({
    //     event: 'RelaySDP',
    //     data: {
    //       peerId: user.data.userId,
    //       sessionDescription: 'T2h0ljI5dfdfdfdfdfdfdfdfdfdfdfdfdfdfdfdf'
    //     }
    //   })
    // )

    // delay(2000)
    // socket.send(
    //   JSON.stringify({
    //     event: 'RelayICE',
    //     data: {
    //       peerId: user.data.userId,
    //       iceCandidate: 'T2h0ljI5dfdfdfdfdfdfdfdfdfdfdfdfdfdfdfdf'
    //     }
    //   })
    // )

    // socket.send(
    //   JSON.stringify({
    //     event: 'GetRooms'
    //   })
    // )

    // setTimeout(() => {
    //   socket.send(
    //     JSON.stringify({
    //       event: 'Leave',
    //       data: {
    //         roomId: chat.data.chatId
    //       }
    //     })
    //   )
    // }, 2000)

    socket.onmessage = async (event: any) => {
      console.log(JSON.parse(event.data))
    }
  }
}

onMounted(async () => {
  // testUsers()
  // testChats()
  handleSocketConnection()
})
</script>
