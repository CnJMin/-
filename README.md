<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>卡牌消除游戏</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background-color: #f9f9f9;
        }

        #game {
            width: 900px;
            height: 600px;
            margin: 50px auto;
            background-color: #fff;
            box-shadow: 0px 0px 10px #ccc;
            position: relative;
        }

        #cards {
            width: 600px;
            height: 550px;
            margin: 25px auto;
            position: relative;
        }

        .card {
            width: 180px;
            height: 250px;
            position: absolute;
            top: 0;
            left: 0;
            background-size: cover;
            cursor: pointer;
        }

        .slot {
            width: 180px;
            height: 250px;
            position: absolute;
            top: 310px;
            left: 0;
            border: 2px dashed #ccc;
            background-color: #f2f2f2;
        }

        .slot.active {
            border-color: #e74c3c;
        }

        .message {
            position: absolute;
            top: 150px;
            left: 150px;
            font-size: 36px;
            color: #2ecc71;
            font-weight: bold;
            text-align: center;
            display: none;
        }

        .button {
            position: absolute;
            top: 500px;
            left: 750px;
            width: 100px;
            height: 50px;
            background-color: #2ecc71;
            color: #fff;
            font-size: 18px;
            text-align: center;
            line-height: 50px;
            cursor: pointer;
        }

        .button:hover {
            background-color: #27ae60;
        }
    </style>
</head>
<body>
    <div id="game">
        <div id="cards"></div>
        <div class="slot"></div>
        <div class="slot"></div>
        <div class="slot"></div>
        <div class="slot"></div>
        <div class="slot"></div>
        <div class="slot"></div>
        <div class="slot"></div>
        <div class="message"></div>
        <div class="button">重新开始</div>
    </div>
</body>
</html>
<script>
// 检查是否已有三张相同的卡牌放在同一槽中
function checkForMatches(slotIndex) {
  const slotCards = slots[slotIndex].children;
  const cardTypes = Array.from(slotCards).map((card) => card.dataset.type);

  // 找出当前槽中出现了3张相同卡牌的索引
  const matchIndexes = [];
  for (let i = 0; i < cardTypes.length - 2; i++) {
    const slice = cardTypes.slice(i, i + 3);
    if (slice.every((type) => type === slice[0])) {
      matchIndexes.push(i);
    }
  }

  // 根据索引移除卡牌并增加分数
  if (matchIndexes.length > 0) {
    matchIndexes.forEach((index) => {
      for (let i = 0; i < 3; i++) {
        const card = slotCards[index];
        score += 10;
        card.remove();
      }
    });
  }
}
function startGame() {
  const icons = ["🐶", "🐱", "🐭", "🐹", "🐰", "🦊", "🐻", "🐼"];
  initGame(icons);
}

function initGame(icons) {
  const cardIcons = [];
  const cardList = document.querySelector(".card-list");
  const slots = document.querySelectorAll(".slot");

  // 生成卡牌图标列表
  for (let i = 0; i < icons.length; i++) {
    for (let j = 0; j < 3; j++) {
      cardIcons.push(icons[i]);
    }
  }
  // 随机打乱卡牌图标顺序
  cardIcons.sort(() => Math.random() - 0.5);
  // 初始化卡牌列表
  for (let i = 0; i < cardIcons.length; i++) {
    const card = createCard(cardIcons[i]);
    cardList.appendChild(card);
  }
  // 绑定拖拽事件
  cardList.addEventListener("dragstart", dragStart);
  cardList.addEventListener("dragend", dragEnd);
  // 初始化卡槽
  for (let i = 0; i < slots.length; i++) {
    const slot = slots[i];
    slot.addEventListener("dragover", dragOver);
    slot.addEventListener("dragenter", dragEnter);
    slot.addEventListener("dragleave", dragLeave);
    slot.addEventListener("drop", dragDrop);
  }
}


// 开始游戏
function startGame() {
  initGame();
}

startGame();
// 定义卡牌的数量和种类
const cardNum = 15 // 卡牌数量
const cardTypeNum = 10 // 卡牌种类数量

// 创建卡牌对象
class Card {
  constructor(type) {
    this.type = type // 卡牌的种类
    this.el = document.createElement('div') // 卡牌对应的DOM元素
    this.el.className = `card card-type-${type}`
  }

  // 将卡牌插入到指定容器中
  mount(container) {
    container.appendChild(this.el)
  }

  // 从指定容器中移除卡牌
  unmount(container) {
    container.removeChild(this.el)
  }

  // 将卡牌放置到指定位置
  setPosition(x, y) {
    this.el.style.left = x + 'px'
    this.el.style.top = y + 'px'
  }
}

// 创建卡牌并将卡牌添加到页面中
const cards = []
const cardContainer = document.querySelector('.card-container')
for (let i = 0; i < cardNum; i++) {
  const type = Math.floor(i / 3) % cardTypeNum
  const card = new Card(type)
  card.setPosition(Math.random() * 600, Math.random() * 300)
  card.mount(cardContainer)
  cards.push(card)
}

// 定义卡槽对象
class Slot {
  constructor(el) {
    this.el = el // 卡槽对应的DOM元素
    this.cards = [] // 卡槽中的卡牌
  }

  // 将卡牌拖拽到卡槽中
  drop(card) {
    this.cards.push(card)
    card.unmount(cardContainer)
    card.mount(this.el)
    if (this.cards.length === 3) {
      const types = this.cards.map(card => card.type)
      if (types.every(type => type === types[0])) {
        this.clear()
      }
    }
  }

  // 清空卡槽中的卡牌
  clear() {
    this.cards.forEach(card => card.unmount(this.el))
    this.cards = []
  }
}

// 创建卡槽并将卡槽添加到页面中
const slots = []
const slotEls = document.querySelectorAll('.slot')
slotEls.forEach((el) => {
  const slot = new Slot(el)
  slots.push(slot)
})

// 绑定卡牌的拖拽事件
let draggingCard = null
let draggingOffsetX = 0
let draggingOffsetY = 0
cards.forEach((card) => {
  card.el.addEventListener('mousedown', (e) => {
    draggingCard = card
    draggingOffsetX = e.offsetX
    draggingOffsetY = e.offsetY
  })

  card.el.addEventListener('mouseup', () => {
    draggingCard = null
  })

  card.el.addEventListener('mousemove', (e) => {
    if (draggingCard) {
      const x = e.clientX - draggingOffsetX
      const y = e.clientY - draggingOffsetY
      draggingCard.setPosition(x, y)
    }
  })
})
// 判断是否满足消除条件
function checkElimination() {
  for (let i = 0; i < slots.length; i++) {
    const slotCards = slots[i].cards
    if (slotCards.length < 3) continue
    const icon = slotCards[0].icon
    let isSame = true
    for (let j = 1; j < slotCards.length; j++) {
      if (slotCards[j].icon !== icon) {
        isSame = false
        break
      }
    }
    if (isSame) {
      for (let j = 0; j < slotCards.length; j++) {
        const card = slotCards[j]
        const index = cards.indexOf(card)
        cards.splice(index, 1)
        card.el.remove()
      }
      slots[i].clear()
      checkElimination()
      break
    }
  }
}

// 游戏结束
function gameEnd() {
  alert('游戏结束，胜利！')
}

// 检查是否游戏胜利
function checkGameEnd() {
  if (cards.length === 0) {
    gameEnd()
  }
}

// 初始化卡牌和卡槽
function initCardsAndSlots() {
  const iconIndices = Array.from({ length: ICON_COUNT }, (_, i) => i)
  const icons = iconIndices.reduce((result, index) => {
    const img = new Image()
    img.src = `icons/${index}.png`
    result.push(img)
    return result
  }, [])
  for (let i = 0; i < cardCount; i++) {
    const iconIndex = Math.floor(i / 3)
    const icon = icons[iconIndex]
    const card = new Card(icon)
    cards.push(card)
  }
  for (let i = 0; i < SLOT_COUNT; i++) {
    const slot = new Slot()
    slots.push(slot)
  }
}

initCardsAndSlots()
render()
document.addEventListener('mouseup', () => {
  if (draggingCard) {
    const x = draggingCard.position.x
    const y = draggingCard.position.y
    let slot = getDropSlot(x, y)
    if (slot) {
      // check if slot already has two cards of same type
      const sameCards = slot.cards.filter((card) => card.icon === draggingCard.icon)
      if (sameCards.length >= 2) {
        // remove cards with same type from the slot
        sameCards.forEach((card) => {
          card.remove()
        })
        // remove dragging card
        draggingCard.remove()
        // check if there are no more cards in the game
        if (cards.length === 0) {
          showWinMessage()
        }
      } else {
        // add dragging card to the slot
        draggingCard.setSlot(slot)
      }
    } else {
      // put dragging card back to its original position
      draggingCard.setPosition(draggingCard.originalPosition.x, draggingCard.originalPosition.y)
    }
    draggingCard = null
    draggingOffsetX = null
    draggingOffsetY = null
  }
})
// 添加卡槽
for (let i = 0; i < 7; i++) {
  const slot = new Slot()
  slot.setPosition((i + 1) * (CARD_WIDTH + CARD_MARGIN) + BOARD_PADDING_LEFT, BOARD_HEIGHT + BOARD_PADDING_TOP + CARD_MARGIN)
  slot.el.addEventListener('mouseup', () => {
    if (draggingCard) {
      const overlappingSlot = slots.find((slot) => slot.overlaps(draggingCard))
      if (overlappingSlot) {
        overlappingSlot.addCard(draggingCard)
        if (checkMatch(overlappingSlot.cards)) {
          removeCards(overlappingSlot.cards)
        }
      } else {
        draggingCard.returnToOriginalPosition()
      }
      draggingCard = null
    }
  })
  slots.push(slot)
}
// 拖拽结束
document.addEventListener('mouseup', (e) => {
  if (draggingCard) {
    // 寻找合法的卡槽
    let targetSlot = null
    for (let i = 0; i < slots.length; i++) {
      const slot = slots[i]
      if (slot.isPointInside(e.clientX, e.clientY) && slot.isCardValid(draggingCard)) {
        targetSlot = slot
        break
      }
    }
    // 如果有合法的卡槽，则将卡牌放入卡槽中
    if (targetSlot) {
      targetSlot.addCard(draggingCard)
      // 检查卡槽中的卡牌是否可以消除
      const sameCards = targetSlot.getSameCards()
      if (sameCards) {
        for (const card of sameCards) {
          card.el.remove()
        }
        targetSlot.clearCards()
        checkGameStatus()
      }
    } else {
      // 如果没有合法的卡槽，则将卡牌返回到原来的位置
      draggingCard.returnToOriginalPosition()
    }
    // 取消卡牌的拖拽状态
    draggingCard = null
  }
})

// 检查游戏状态
function checkGameStatus() {
  let isGameFinished = true
  for (const slot of slots) {
    if (slot.cards.length > 0) {
      isGameFinished = false
      break
    }
  }
  if (isGameFinished) {
    alert('恭喜你，游戏通关！')
  }
}
function removeCard(index) {
  const cardsToRemove = []
  for (let i = 0; i < cards.length; i++) {
    if (cards[i].index === index) {
      cardsToRemove.push(cards[i])
    }
  }
  cardsToRemove.forEach(card => {
    card.el.remove()
    cards.splice(cards.indexOf(card), 1)
  })
}

function checkForMatches() {
  const cardCount = {}
  cards.forEach(card => {
    if (!cardCount[card.type]) {
      cardCount[card.type] = 0
    }
    cardCount[card.type]++
  })
  for (const type in cardCount) {
    if (cardCount[type] >= 3) {
      for (let i = 0; i < cards.length; i++) {
        if (cards[i].type === type) {
          removeCard(cards[i].index)
          i--
        }
      }
    }
  }
  if (cards.length === 0) {
    alert('You win!')
  }
}
function removeCards(cards) {
  for (const card of cards) {
    card.destroy()
  }
  remainingCards = remainingCards.filter((card) => !cards.includes(card))
}

function checkWin() {
  if (remainingCards.length === 0) {
    alert('你赢了！')
    resetGame()
  }
}

function resetGame() {
  for (const slot of slots) {
    slot.empty()
  }
  for (const card of remainingCards) {
    card.destroy()
  }
  remainingCards = []
  initGame()
}

initGame()
</script>

	
