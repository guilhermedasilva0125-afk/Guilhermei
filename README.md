-- Script Completo: Classes + Trens + Loja Inteligente + Sala + Bonds
-- Interface rebaixada + Botões de sala lado a lado em tamanho compacto

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

if playerGui:FindFirstChild("DeltaFullGUI") then
    playerGui.DeltaFullGUI:Destroy()
end
if CoreGui:FindFirstChild("BondTrackerGui") then
    CoreGui.BondTrackerGui:Destroy()
end

-------------------------------------------------
-- DADOS
-------------------------------------------------
local classes = {
    {en = "Horse", pt = "Cavalo"}, {en = "Cowboy", pt = "Caubói"}, {en = "Ghost", pt = "Fantasma"},
    {en = "Werewolf", pt = "Lobisomem"}, {en = "Vampire", pt = "Vampiro"}, {en = "Packmaster", pt = "Líder da Matilha"},
    {en = "Survivalist", pt = "Sobrevivente"}, {en = "Doctor", pt = "Médico"}, {en = "Arsonist", pt = "Incendiário"},
    {en = "Miner", pt = "Mineiro"}, {en = "Corrosioneer", pt = "Corrosivo"}, {en = "Alamo", pt = "Alamo"},
    {en = "Necromancer", pt = "Necromante"}, {en = "HighRoller", pt = "Apostador"}, {en = "Musician", pt = "Músico"},
    {en = "MunitionsExpert", pt = "Especialista em Munição"}, {en = "Conductor", pt = "Maestro"}, {en = "Alien", pt = "Alienígena"},
    {en = "Cursed", pt = "Amaldiçoado"}, {en = "President", pt = "Presidente"}, {en = "Tycoon", pt = "Magnata"},
    {en = "Priest", pt = "Padre"}, {en = "King", pt = "Rei"}, {en = "Knight", pt = "Cavaleiro"},
    {en = "Demolitionist", pt = "Demolidor"}, {en = "Milkman", pt = "Leiteiro"}, {en = "Zombie", pt = "Zumbi"},
    {en = "Elf", pt = "Elfo"}, {en = "Hunter", pt = "Caçador"}, {en = "Ironclad", pt = "Blindado"},
    {en = "Santa", pt = "Papai Noel"}, {en = "Krampus", pt = "Krampus"}, {en = "Frankenstein", pt = "Frankenstein"},
}

local trains = {
    {id = "presidential", name = "Presidential", pt = "Presidencial"},
    {id = "christmas_event_2025", name = "Christmas 2025", pt = "Natal 2025"},
    {id = "wooden", name = "Wooden", pt = "Madeira"},
    {id = "oddthings", name = "Odd Things", pt = "Coisas Estranhas"},
    {id = "dracula", name = "Dracula", pt = "Drácula"},
    {id = "locomotive", name = "Locomotive", pt = "Locomotiva"},
    {id = "cattle", name = "Cattle", pt = "Gado"},
    {id = "passenger", name = "Passenger", pt = "Passageiro"},
    {id = "golden", name = "Golden", pt = "Dourado"},
    {id = "frost", name = "Frost", pt = "Gelo"},
    {id = "armored", name = "Armored", pt = "Blindado"},
    {id = "ghost", name = "Ghost", pt = "Fantasma"},
}

local shopItems = {
    {name = "Rifle", id = "rifle", price = 5},
    {name = "Shotgun", id = "shotgun", price = 3},
    {name = "Revolver", id = "revolver", price = 3},
    {name = "Ammo Medium", id = "ammo_medium", price = 2},
    {name = "Ammo Shells", id = "ammo_shells", price = 2},
    {name = "Ammo Light", id = "ammo_light", price = 2},
    {name = "Molotov", id = "molotov", price = 3},
    {name = "Coal", id = "coal", price = 2},
    {name = "Snake Oil", id = "snake_oil", price = 2},
    {name = "Bandage", id = "bandage", price = 1},
    {name = "Armor Chestplate", id = "armor_chestplate", price = 10},
    {name = "Armor Helmet", id = "armor_helmet", price = 7},
}

local salas = {
    {name = "Sala 1", pos = Vector3.new(62.8, 7.4, -139.3)},
    {name = "Sala 2", pos = Vector3.new(62.8, 7.4, -124.3)},
    {name = "Sala 3", pos = Vector3.new(62.8, 7.4, -109.3)},
    {name = "Sala 4", pos = Vector3.new(62.8, 7.4, -94.3)},
}

local gameModes = {
    {id = "Normal", name = "Normal"},
    {id = "BiteSized", name = "Bite Sized"},
    {id = "DeepFreeze", name = "Deep Freeze"},
    {id = "Endless", name = "Endless"},
    {id = "Nightmare", name = "Nightmare"},
    {id = "ScorchedEarth", name = "Scorched Earth"},
}

local selectedMode = "Normal"

local function EquipClass(name)
    game:GetService("ReplicatedStorage"):WaitForChild("Shared"):WaitForChild("Universe"):WaitForChild("Network"):WaitForChild("RemoteEvent"):WaitForChild("EquipClass"):FireServer(name)
end

local function EquipTrain(id)
    game:GetService("ReplicatedStorage"):WaitForChild("Shared"):WaitForChild("Universe"):WaitForChild("Network"):WaitForChild("RemoteEvent"):WaitForChild("EquipTrain"):FireServer(id)
end

local function RequestBuy(id)
    game:GetService("ReplicatedStorage"):WaitForChild("Shared"):WaitForChild("Universe"):WaitForChild("Network"):WaitForChild("RemoteEvent"):WaitForChild("RequestBuy"):FireServer(id)
end

local function TeleportToSala(pos)
    local char = player.Character
    if not char then return false end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return false end
    hrp.CFrame = CFrame.new(pos + Vector3.new(0, 2, 0))
    return true
end

local function CreateParty(maxMembers, trainId, gameMode, isPrivate)
    maxMembers = maxMembers or 1
    trainId = trainId or "default"
    gameMode = gameMode or "Normal"
    isPrivate = isPrivate or false

    local args = {
        {
            isPrivate = isPrivate,
            maxMembers = maxMembers,
            trainId = trainId,
            gameMode = gameMode
        }
    }

    pcall(function()
        game:GetService("ReplicatedStorage")
            :WaitForChild("Shared")
            :WaitForChild("Universe")
            :WaitForChild("Network")
            :WaitForChild("RemoteEvent")
            :WaitForChild("CreateParty")
            :FireServer(unpack(args))
    end)
end

-------------------------------------------------
-- SISTEMA DE BONDS
-------------------------------------------------
local function GetBondElement()
    local pg = player:FindFirstChild("PlayerGui")
    if not pg then return nil end

    local display = pg:FindFirstChild("BondDisplay") 
                 or pg:FindFirstChild("BongDisplay") 
                 or pg:FindFirstChild("BondsDisplay")
    if not display then return nil end

    local bondInfo = display:FindFirstChild("BondInfo") 
                  or display:FindFirstChild("Info") 
                  or display:FindFirstChild("Bonds")
    if not bondInfo then return nil end

    return bondInfo:FindFirstChild("BondCount") 
        or bondInfo:FindFirstChild("Count") 
        or bondInfo:FindFirstChild("Amount") 
        or bondInfo:FindFirstChild("Value")
end

local function GetBondCount()
    local bondCount = GetBondElement()
    if not bondCount then return "Aguardando..." end

    if bondCount:IsA("TextLabel") or bondCount:IsA("TextButton") or bondCount:IsA("TextBox") then
        return bondCount.Text
    elseif bondCount:IsA("NumberValue") or bondCount:IsA("IntValue") or bondCount:IsA("StringValue") then
        return tostring(bondCount.Value)
    end

    return "..."
end

local function GetBondNumber()
    local text = GetBondCount()
    if not text or text == "..." or text == "Aguardando..." then return 0 end
    local clean = string.gsub(tostring(text), "[^0-9]", "")
    return tonumber(clean) or 0
end

local function ModifyGameBondUI(valueText)
    pcall(function()
        local bondCount = GetBondElement()
        if bondCount then
            if bondCount:IsA("TextLabel") or bondCount:IsA("TextButton") or bondCount:IsA("TextBox") then
                bondCount.Text = tostring(valueText)
            elseif bondCount:IsA("NumberValue") or bondCount:IsA("IntValue") or bondCount:IsA("StringValue") then
                bondCount.Value = tonumber(valueText) or valueText
            end
        end
    end)
end

-------------------------------------------------
-- GUI PRINCIPAL
-------------------------------------------------
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "DeltaFullGUI"
screenGui.ResetOnSpawn = false
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.Parent = playerGui

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 380, 0, 640)
-- Posição rebaixada no ecrã (Y: 0.74 em vez de 0.68)
mainFrame.Position = UDim2.new(0.5, -190, 0.74, -320)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 28)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Parent = screenGui

Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 16)
local stroke = Instance.new("UIStroke", mainFrame)
stroke.Color = Color3.fromRGB(0, 160, 255)
stroke.Thickness = 2

local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 42)
titleBar.BackgroundColor3 = Color3.fromRGB(0, 110, 200)
titleBar.Parent = mainFrame
Instance.new("UICorner", titleBar).CornerRadius = UDim.new(0, 16)

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -90, 1, 0)
title.BackgroundTransparency = 1
title.Text = "  Classes + Trens + Loja + Sala"
title.TextColor3 = Color3.new(1,1,1)
title.Font = Enum.Font.GothamBold
title.TextSize = 14
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = titleBar

local hideBtn = Instance.new("TextButton")
hideBtn.Size = UDim2.new(0, 38, 0, 42)
hideBtn.Position = UDim2.new(1, -82, 0, 0)
hideBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
hideBtn.Text = "–"
hideBtn.TextColor3 = Color3.new(1,1,1)
hideBtn.Font = Enum.Font.GothamBold
hideBtn.TextSize = 22
hideBtn.Parent = titleBar
Instance.new("UICorner", hideBtn).CornerRadius = UDim.new(0, 10)

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 42, 0, 42)
closeBtn.Position = UDim2.new(1, -42, 0, 0)
closeBtn.BackgroundColor3 = Color3.fromRGB(200, 45, 45)
closeBtn.Text = "X"
closeBtn.TextColor3 = Color3.new(1,1,1)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 18
closeBtn.Parent = titleBar
Instance.new("UICorner", closeBtn).CornerRadius = UDim.new(0, 16)

closeBtn.MouseButton1Click:Connect(function() 
    screenGui:Destroy()
end)

local reopenBtn = Instance.new("TextButton")
reopenBtn.Size = UDim2.new(0, 50, 0, 50)
reopenBtn.Position = UDim2.new(0, 12, 0.72, -25)
reopenBtn.BackgroundColor3 = Color3.fromRGB(0, 110, 200)
reopenBtn.Text = "☰"
reopenBtn.TextColor3 = Color3.new(1,1,1)
reopenBtn.Font = Enum.Font.GothamBold
reopenBtn.TextSize = 22
reopenBtn.Visible = false
reopenBtn.Parent = screenGui
Instance.new("UICorner", reopenBtn).CornerRadius = UDim.new(0, 14)

hideBtn.MouseButton1Click:Connect(function()
    mainFrame.Visible = false
    reopenBtn.Visible = true
end)
reopenBtn.MouseButton1Click:Connect(function()
    mainFrame.Visible = true
    reopenBtn.Visible = false
end)

local dragging, dragStart, startPos
titleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then dragging = false end
        end)
    end
end)
UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-------------------------------------------------
-- CONTADOR DE BONDS + BOTÃO DE MODIFICAR
-------------------------------------------------
local bondsFrame = Instance.new("Frame")
bondsFrame.Size = UDim2.new(1, -16, 0, 34)
bondsFrame.Position = UDim2.new(0, 8, 0, 48)
bondsFrame.BackgroundColor3 = Color3.fromRGB(25, 35, 50)
bondsFrame.Parent = mainFrame
Instance.new("UICorner", bondsFrame).CornerRadius = UDim.new(0, 10)

local bondsLabel = Instance.new("TextLabel")
bondsLabel.Size = UDim2.new(1, -135, 1, 0)
bondsLabel.Position = UDim2.new(0, 8, 0, 0)
bondsLabel.BackgroundTransparency = 1
bondsLabel.Text = "💰 Bonds: ..."
bondsLabel.TextColor3 = Color3.fromRGB(100, 255, 150)
bondsLabel.Font = Enum.Font.GothamBold
bondsLabel.TextSize = 14
bondsLabel.TextXAlignment = Enum.TextXAlignment.Left
bondsLabel.Parent = bondsFrame

local modBondBtn = Instance.new("TextButton")
modBondBtn.Size = UDim2.new(0, 120, 0, 26)
modBondBtn.Position = UDim2.new(1, -124, 0, 4)
modBondBtn.BackgroundColor3 = Color3.fromRGB(0, 140, 220)
modBondBtn.Text = "Modificar Bond"
modBondBtn.TextColor3 = Color3.new(1,1,1)
modBondBtn.Font = Enum.Font.GothamBold
modBondBtn.TextSize = 11
modBondBtn.Parent = bondsFrame
Instance.new("UICorner", modBondBtn).CornerRadius = UDim.new(0, 8)

-- POPUP MODIFICAR BOND
local modModal = Instance.new("Frame")
modModal.Size = UDim2.new(0, 290, 0, 190)
modModal.Position = UDim2.new(0.5, -145, 0.5, -95)
modModal.BackgroundColor3 = Color3.fromRGB(25, 27, 38)
modModal.BorderSizePixel = 0
modModal.Visible = false
modModal.ZIndex = 10
modModal.Parent = mainFrame
Instance.new("UICorner", modModal).CornerRadius = UDim.new(0, 12)

local modStroke = Instance.new("UIStroke", modModal)
modStroke.Color = Color3.fromRGB(255, 170, 0)
modStroke.Thickness = 2

local modTitle = Instance.new("TextLabel")
modTitle.Size = UDim2.new(1, 0, 0, 30)
modTitle.BackgroundTransparency = 1
modTitle.Text = "Modificar Bond"
modTitle.TextColor3 = Color3.new(1,1,1)
modTitle.Font = Enum.Font.GothamBold
modTitle.TextSize = 14
modTitle.ZIndex = 11
modTitle.Parent = modModal

local warningLabel = Instance.new("TextLabel")
warningLabel.Size = UDim2.new(1, -16, 0, 42)
warningLabel.Position = UDim2.new(0, 8, 0, 28)
warningLabel.BackgroundTransparency = 1
warningLabel.Text = "⚠️ AVISO: Alteração APENAS VISUAL e não altera no servidor."
warningLabel.TextColor3 = Color3.fromRGB(255, 180, 80)
warningLabel.Font = Enum.Font.Gotham
warningLabel.TextSize = 11
warningLabel.TextWrapped = true
warningLabel.ZIndex = 11
warningLabel.Parent = modModal

local inputBondBox = Instance.new("TextBox")
inputBondBox.Size = UDim2.new(1, -16, 0, 32)
inputBondBox.Position = UDim2.new(0, 8, 0, 75)
inputBondBox.BackgroundColor3 = Color3.fromRGB(38, 40, 55)
inputBondBox.PlaceholderText = "Digite a quantidade visual..."
inputBondBox.PlaceholderColor3 = Color3.fromRGB(150, 150, 160)
inputBondBox.Text = ""
inputBondBox.TextColor3 = Color3.new(1,1,1)
inputBondBox.Font = Enum.Font.GothamBold
inputBondBox.TextSize = 14
inputBondBox.ZIndex = 11
inputBondBox.Parent = modModal
Instance.new("UICorner", inputBondBox).CornerRadius = UDim.new(0, 8)

local applyBtn = Instance.new("TextButton")
applyBtn.Size = UDim2.new(0.46, 0, 0, 32)
applyBtn.Position = UDim2.new(0, 8, 0, 115)
applyBtn.BackgroundColor3 = Color3.fromRGB(0, 160, 85)
applyBtn.Text = "Aplicar"
applyBtn.TextColor3 = Color3.new(1,1,1)
applyBtn.Font = Enum.Font.GothamBold
applyBtn.TextSize = 13
applyBtn.ZIndex = 11
applyBtn.Parent = modModal
Instance.new("UICorner", applyBtn).CornerRadius = UDim.new(0, 8)

local resetBtn = Instance.new("TextButton")
resetBtn.Size = UDim2.new(0.46, 0, 0, 32)
resetBtn.Position = UDim2.new(0.54, 0, 0, 115)
resetBtn.BackgroundColor3 = Color3.fromRGB(180, 55, 55)
resetBtn.Text = "Resetar"
resetBtn.TextColor3 = Color3.new(1,1,1)
resetBtn.Font = Enum.Font.GothamBold
resetBtn.TextSize = 13
resetBtn.ZIndex = 11
resetBtn.Parent = modModal
Instance.new("UICorner", resetBtn).CornerRadius = UDim.new(0, 8)

local closeModBtn = Instance.new("TextButton")
closeModBtn.Size = UDim2.new(1, -16, 0, 24)
closeModBtn.Position = UDim2.new(0, 8, 0, 155)
closeModBtn.BackgroundTransparency = 1
closeModBtn.Text = "Fechar"
closeModBtn.TextColor3 = Color3.fromRGB(180, 180, 190)
closeModBtn.Font = Enum.Font.Gotham
closeModBtn.TextSize = 12
closeModBtn.ZIndex = 11
closeModBtn.Parent = modModal

local visualBondOverride = nil

modBondBtn.MouseButton1Click:Connect(function()
    modModal.Visible = not modModal.Visible
end)

closeModBtn.MouseButton1Click:Connect(function()
    modModal.Visible = false
end)

applyBtn.MouseButton1Click:Connect(function()
    local val = inputBondBox.Text
    if val and val ~= "" then
        visualBondOverride = val
        ModifyGameBondUI(val)
        game:GetService("StarterGui"):SetCore("SendNotification", {
            Title = "Aviso Visual",
            Text = "Bond alterado para: " .. val,
            Duration = 3
        })
    end
    modModal.Visible = false
end)

resetBtn.MouseButton1Click:Connect(function()
    visualBondOverride = nil
    inputBondBox.Text = ""
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Aviso Visual",
        Text = "Restaurado ao valor real.",
        Duration = 3
    })
    modModal.Visible = false
end)

task.spawn(function()
    while screenGui and screenGui.Parent do
        if visualBondOverride then
            bondsLabel.Text = "💰 Bonds: " .. tostring(visualBondOverride)
            ModifyGameBondUI(visualBondOverride)
        else
            bondsLabel.Text = "💰 Bonds: " .. tostring(GetBondCount())
        end
        task.wait(0.2)
    end
end)

-------------------------------------------------
-- ABAS
-------------------------------------------------
local tabFrame = Instance.new("Frame")
tabFrame.Size = UDim2.new(1, -16, 0, 34)
tabFrame.Position = UDim2.new(0, 8, 0, 88)
tabFrame.BackgroundTransparency = 1
tabFrame.Parent = mainFrame

local function createTab(text, pos)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.25, -4, 1, 0)
    btn.Position = pos
    btn.BackgroundColor3 = Color3.fromRGB(45, 47, 65)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(180, 180, 190)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 13
    btn.Parent = tabFrame
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 10)
    return btn
end

local classTab = createTab("Classes", UDim2.new(0, 0, 0, 0))
local trainTab = createTab("Trens", UDim2.new(0.25, 1, 0, 0))
local shopTab = createTab("Loja", UDim2.new(0.50, 2, 0, 0))
local salaTab = createTab("Sala", UDim2.new(0.75, 3, 0, 0))

-------------------------------------------------
-- CONTEÚDOS
-------------------------------------------------
local function createContent()
    local f = Instance.new("Frame")
    f.Size = UDim2.new(1, -16, 1, -135)
    f.Position = UDim2.new(0, 8, 0, 128)
    f.BackgroundTransparency = 1
    f.Visible = false
    f.Parent = mainFrame
    return f
end

local classContent = createContent()
local trainContent = createContent()
local shopContent = createContent()
local salaContent = createContent()
classContent.Visible = true

-- ========== CLASSES ==========
local classSearch = Instance.new("TextBox")
classSearch.Size = UDim2.new(1, 0, 0, 34)
classSearch.BackgroundColor3 = Color3.fromRGB(32, 34, 48)
classSearch.Text = ""
classSearch.PlaceholderText = "🔍 Pesquisar classe..."
classSearch.PlaceholderColor3 = Color3.fromRGB(140,140,155)
classSearch.TextColor3 = Color3.new(1,1,1)
classSearch.Font = Enum.Font.Gotham
classSearch.TextSize = 14
classSearch.ClearTextOnFocus = false
classSearch.Parent = classContent
Instance.new("UICorner", classSearch).CornerRadius = UDim.new(0, 10)

local classScroll = Instance.new("ScrollingFrame")
classScroll.Size = UDim2.new(1, 0, 1, -42)
classScroll.Position = UDim2.new(0, 0, 0, 42)
classScroll.BackgroundTransparency = 1
classScroll.ScrollBarThickness = 5
classScroll.Parent = classContent

local classGrid = Instance.new("UIGridLayout")
classGrid.CellSize = UDim2.new(0, 108, 0, 40)
classGrid.CellPadding = UDim2.new(0, 6, 0, 6)
classGrid.HorizontalAlignment = Enum.HorizontalAlignment.Center
classGrid.Parent = classScroll

local classBtns = {}
for i, data in ipairs(classes) do
    local btn = Instance.new("TextButton")
    btn.BackgroundColor3 = Color3.fromRGB(38, 40, 55)
    btn.Text = data.en
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 13
    btn.Parent = classScroll
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 9)
    btn.MouseButton1Click:Connect(function()
        EquipClass(data.en)
        game:GetService("StarterGui"):SetCore("SendNotification", {Title = "Classe", Text = data.en, Duration = 2})
    end)
    table.insert(classBtns, {btn = btn, en = data.en, pt = data.pt})
end

classSearch:GetPropertyChangedSignal("Text"):Connect(function()
    local t = string.lower(classSearch.Text)
    for _, d in ipairs(classBtns) do
        d.btn.Visible = t == "" or string.find(string.lower(d.en), t) or string.find(string.lower(d.pt), t)
    end
end)

-- ========== TRENS ==========
local trainSearch = Instance.new("TextBox")
trainSearch.Size = UDim2.new(1, 0, 0, 34)
trainSearch.BackgroundColor3 = Color3.fromRGB(32, 34, 48)
trainSearch.Text = ""
trainSearch.PlaceholderText = "🔍 Pesquisar trem..."
trainSearch.PlaceholderColor3 = Color3.fromRGB(140,140,155)
trainSearch.TextColor3 = Color3.new(1,1,1)
trainSearch.Font = Enum.Font.Gotham
trainSearch.TextSize = 14
trainSearch.ClearTextOnFocus = false
trainSearch.Parent = trainContent
Instance.new("UICorner", trainSearch).CornerRadius = UDim.new(0, 10)

local trainScroll = Instance.new("ScrollingFrame")
trainScroll.Size = UDim2.new(1, 0, 1, -42)
trainScroll.Position = UDim2.new(0, 0, 0, 42)
trainScroll.BackgroundTransparency = 1
trainScroll.ScrollBarThickness = 5
trainScroll.Parent = trainContent

local trainGrid = Instance.new("UIGridLayout")
trainGrid.CellSize = UDim2.new(0, 108, 0, 54)
trainGrid.CellPadding = UDim2.new(0, 6, 0, 6)
trainGrid.HorizontalAlignment = Enum.HorizontalAlignment.Center
trainGrid.Parent = trainScroll

local trainBtns = {}
for i, data in ipairs(trains) do
    local btn = Instance.new("TextButton")
    btn.BackgroundColor3 = Color3.fromRGB(38, 40, 55)
    btn.Text = data.name
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 13
    btn.TextWrapped = true
    btn.Parent = trainScroll
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 9)
    btn.MouseButton1Click:Connect(function()
        EquipTrain(data.id)
        game:GetService("StarterGui"):SetCore("SendNotification", {Title = "Trem", Text = data.name, Duration = 2})
    end)
    table.insert(trainBtns, {btn = btn, name = data.name, pt = data.pt})
end

trainSearch:GetPropertyChangedSignal("Text"):Connect(function()
    local t = string.lower(trainSearch.Text)
    for _, d in ipairs(trainBtns) do
        d.btn.Visible = t == "" or string.find(string.lower(d.name), t) or string.find(string.lower(d.pt), t)
    end
end)

-- ========== LOJA ==========
local shopSearch = Instance.new("TextBox")
shopSearch.Size = UDim2.new(1, 0, 0, 34)
shopSearch.BackgroundColor3 = Color3.fromRGB(32, 34, 48)
shopSearch.Text = ""
shopSearch.PlaceholderText = "🔍 Pesquisar item..."
shopSearch.PlaceholderColor3 = Color3.fromRGB(140,140,155)
shopSearch.TextColor3 = Color3.new(1,1,1)
shopSearch.Font = Enum.Font.Gotham
shopSearch.TextSize = 14
shopSearch.ClearTextOnFocus = false
shopSearch.Parent = shopContent
Instance.new("UICorner", shopSearch).CornerRadius = UDim.new(0, 10)

local qtyFrame = Instance.new("Frame")
qtyFrame.Size = UDim2.new(1, 0, 0, 110)
qtyFrame.Position = UDim2.new(0, 0, 0, 42)
qtyFrame.BackgroundTransparency = 1
qtyFrame.Parent = shopContent

local qtyLabel = Instance.new("TextLabel")
qtyLabel.Size = UDim2.new(0, 40, 0, 28)
qtyLabel.BackgroundTransparency = 1
qtyLabel.Text = "Qtd:"
qtyLabel.TextColor3 = Color3.fromRGB(200,200,210)
qtyLabel.Font = Enum.Font.Gotham
qtyLabel.TextSize = 14
qtyLabel.Parent = qtyFrame

local qtyBox = Instance.new("TextBox")
qtyBox.Size = UDim2.new(0, 50, 0, 28)
qtyBox.Position = UDim2.new(0, 42, 0, 0)
qtyBox.BackgroundColor3 = Color3.fromRGB(32, 34, 48)
qtyBox.Text = "1"
qtyBox.TextColor3 = Color3.new(1,1,1)
qtyBox.Font = Enum.Font.GothamBold
qtyBox.TextSize = 15
qtyBox.Parent = qtyFrame
Instance.new("UICorner", qtyBox).CornerRadius = UDim.new(0, 8)

local plusBtn = Instance.new("TextButton")
plusBtn.Size = UDim2.new(0, 28, 0, 28)
plusBtn.Position = UDim2.new(0, 97, 0, 0)
plusBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 90)
plusBtn.Text = "+"
plusBtn.TextColor3 = Color3.new(1,1,1)
plusBtn.Font = Enum.Font.GothamBold
plusBtn.TextSize = 16
plusBtn.Parent = qtyFrame
Instance.new("UICorner", plusBtn).CornerRadius = UDim.new(0, 8)

local minusBtn = Instance.new("TextButton")
minusBtn.Size = UDim2.new(0, 28, 0, 28)
minusBtn.Position = UDim2.new(0, 130, 0, 0)
minusBtn.BackgroundColor3 = Color3.fromRGB(180, 55, 55)
minusBtn.Text = "-"
minusBtn.TextColor3 = Color3.new(1,1,1)
minusBtn.Font = Enum.Font.GothamBold
minusBtn.TextSize = 16
minusBtn.Parent = qtyFrame
Instance.new("UICorner", minusBtn).CornerRadius = UDim.new(0, 8)

local reserveLabel = Instance.new("TextLabel")
reserveLabel.Size = UDim2.new(0, 70, 0, 28)
reserveLabel.Position = UDim2.new(0, 170, 0, 0)
reserveLabel.BackgroundTransparency = 1
reserveLabel.Text = "Deixar:"
reserveLabel.TextColor3 = Color3.fromRGB(200,200,210)
reserveLabel.Font = Enum.Font.Gotham
reserveLabel.TextSize = 13
reserveLabel.Parent = qtyFrame

local reserveBox = Instance.new("TextBox")
reserveBox.Size = UDim2.new(0, 55, 0, 28)
reserveBox.Position = UDim2.new(0, 235, 0, 0)
reserveBox.BackgroundColor3 = Color3.fromRGB(32, 34, 48)
reserveBox.Text = "0"
reserveBox.TextColor3 = Color3.new(1,1,1)
reserveBox.Font = Enum.Font.GothamBold
reserveBox.TextSize = 14
reserveBox.Parent = qtyFrame
Instance.new("UICorner", reserveBox).CornerRadius = UDim.new(0, 8)

local totalLabel = Instance.new("TextLabel")
totalLabel.Size = UDim2.new(1, 0, 0, 22)
totalLabel.Position = UDim2.new(0, 0, 0, 34)
totalLabel.BackgroundTransparency = 1
totalLabel.Text = "Selecione um item"
totalLabel.TextColor3 = Color3.fromRGB(100, 255, 150)
totalLabel.Font = Enum.Font.GothamBold
totalLabel.TextSize = 13
totalLabel.TextXAlignment = Enum.TextXAlignment.Left
totalLabel.Parent = qtyFrame

local maxLabel = Instance.new("TextLabel")
maxLabel.Size = UDim2.new(1, 0, 0, 18)
maxLabel.Position = UDim2.new(0, 0, 0, 54)
maxLabel.BackgroundTransparency = 1
maxLabel.Text = ""
maxLabel.TextColor3 = Color3.fromRGB(255, 200, 100)
maxLabel.Font = Enum.Font.Gotham
maxLabel.TextSize = 12
maxLabel.TextXAlignment = Enum.TextXAlignment.Left
maxLabel.Parent = qtyFrame

local buyNowBtn = Instance.new("TextButton")
buyNowBtn.Size = UDim2.new(1, 0, 0, 32)
buyNowBtn.Position = UDim2.new(0, 0, 0, 76)
buyNowBtn.BackgroundColor3 = Color3.fromRGB(0, 160, 85)
buyNowBtn.Text = "🛒  COMPRAR AGORA"
buyNowBtn.TextColor3 = Color3.new(1,1,1)
buyNowBtn.Font = Enum.Font.GothamBold
buyNowBtn.TextSize = 15
buyNowBtn.Parent = qtyFrame
Instance.new("UICorner", buyNowBtn).CornerRadius = UDim.new(0, 10)

local selectedItem = nil

local function updateTotal()
    local amount = tonumber(qtyBox.Text) or 1
    local reserve = tonumber(reserveBox.Text) or 0
    local currentBonds = GetBondNumber()
    
    if selectedItem then
        local totalCost = selectedItem.price * amount
        local maxPossible = math.floor(math.max(0, currentBonds - reserve) / selectedItem.price)
        
        totalLabel.Text = amount .. "x " .. selectedItem.name .. " = " .. totalCost .. " Bonds"
        
        if amount > maxPossible then
            maxLabel.Text = "⚠ Máximo possível: " .. maxPossible .. " (você tem " .. currentBonds .. ")"
            maxLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
        else
            maxLabel.Text = "✓ Pode comprar (vai sobrar ~" .. (currentBonds - totalCost) .. ")"
            maxLabel.TextColor3 = Color3.fromRGB(100, 255, 150)
        end
    else
        totalLabel.Text = "Selecione um item"
        maxLabel.Text = ""
    end
end

qtyBox:GetPropertyChangedSignal("Text"):Connect(updateTotal)
reserveBox:GetPropertyChangedSignal("Text"):Connect(updateTotal)

plusBtn.MouseButton1Click:Connect(function()
    local n = tonumber(qtyBox.Text) or 1
    qtyBox.Text = tostring(math.clamp(n + 1, 1, 999))
    updateTotal()
end)

minusBtn.MouseButton1Click:Connect(function()
    local n = tonumber(qtyBox.Text) or 1
    qtyBox.Text = tostring(math.clamp(n - 1, 1, 999))
    updateTotal()
end)

local shopScroll = Instance.new("ScrollingFrame")
shopScroll.Size = UDim2.new(1, 0, 1, -160)
shopScroll.Position = UDim2.new(0, 0, 0, 158)
shopScroll.BackgroundTransparency = 1
shopScroll.ScrollBarThickness = 5
shopScroll.Parent = shopContent

local shopGrid = Instance.new("UIGridLayout")
shopGrid.CellSize = UDim2.new(0, 108, 0, 54)
shopGrid.CellPadding = UDim2.new(0, 6, 0, 6)
shopGrid.HorizontalAlignment = Enum.HorizontalAlignment.Center
shopGrid.Parent = shopScroll

local shopBtns = {}

for i, item in ipairs(shopItems) do
    local btn = Instance.new("TextButton")
    btn.BackgroundColor3 = Color3.fromRGB(38, 40, 55)
    btn.Text = item.name .. "\n" .. item.price .. " Bonds"
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 12
    btn.TextWrapped = true
    btn.Parent = shopScroll
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 9)

    btn.MouseButton1Click:Connect(function()
        selectedItem = item
        updateTotal()
        for _, d in ipairs(shopBtns) do
            d.btn.BackgroundColor3 = Color3.fromRGB(38, 40, 55)
        end
        btn.BackgroundColor3 = Color3.fromRGB(0, 130, 220)
    end)

    table.insert(shopBtns, {btn = btn, name = item.name})
end

buyNowBtn.MouseButton1Click:Connect(function()
    if not selectedItem then
        game:GetService("StarterGui"):SetCore("SendNotification", {
            Title = "Aviso",
            Text = "Selecione um item primeiro!",
            Duration = 3
        })
        return
    end

    local requested = tonumber(qtyBox.Text) or 1
    local reserve = tonumber(reserveBox.Text) or 0
    local currentBonds = GetBondNumber()
    
    local maxPossible = math.floor(math.max(0, currentBonds - reserve) / selectedItem.price)
    local amountToBuy = math.min(requested, maxPossible)

    if amountToBuy <= 0 then
        game:GetService("StarterGui"):SetCore("SendNotification", {
            Title = "Sem Bonds",
            Text = "Você não tem Bonds suficientes!",
            Duration = 3
        })
        return
    end

    if amountToBuy < requested then
        game:GetService("StarterGui"):SetCore("SendNotification", {
            Title = "Ajuste automático",
            Text = "Pediu " .. requested .. ", mas só consegue " .. amountToBuy,
            Duration = 3
        })
    end

    for i = 1, amountToBuy do
        RequestBuy(selectedItem.id)
        task.wait(0.28)
    end

    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Compra Realizada",
        Text = amountToBuy .. "x " .. selectedItem.name .. " = " .. (selectedItem.price * amountToBuy) .. " Bonds",
        Duration = 3
    })
    
    task.wait(0.3)
    updateTotal()
end)

shopSearch:GetPropertyChangedSignal("Text"):Connect(function()
    local t = string.lower(shopSearch.Text)
    for _, d in ipairs(shopBtns) do
        d.btn.Visible = t == "" or string.find(string.lower(d.name), t)
    end
end)

-- ========== SALA (BOTÕES COMPACTOS LADO A LADO) ==========
local salaTitle = Instance.new("TextLabel")
salaTitle.Size = UDim2.new(1, 0, 0, 24)
salaTitle.BackgroundTransparency = 1
salaTitle.Text = "Modo de Jogo + Max Jogadores"
salaTitle.TextColor3 = Color3.fromRGB(180, 220, 255)
salaTitle.Font = Enum.Font.GothamBold
salaTitle.TextSize = 14
salaTitle.Parent = salaContent

local maxFrame = Instance.new("Frame")
maxFrame.Size = UDim2.new(1, 0, 0, 32)
maxFrame.Position = UDim2.new(0, 0, 0, 26)
maxFrame.BackgroundTransparency = 1
maxFrame.Parent = salaContent

local maxLabel = Instance.new("TextLabel")
maxLabel.Size = UDim2.new(0, 70, 1, 0)
maxLabel.BackgroundTransparency = 1
maxLabel.Text = "Max jog:"
maxLabel.TextColor3 = Color3.fromRGB(200,200,210)
maxLabel.Font = Enum.Font.Gotham
maxLabel.TextSize = 13
maxLabel.Parent = maxFrame

local maxBox = Instance.new("TextBox")
maxBox.Size = UDim2.new(0, 40, 0, 26)
maxBox.Position = UDim2.new(0, 72, 0, 3)
maxBox.BackgroundColor3 = Color3.fromRGB(32, 34, 48)
maxBox.Text = "1"
maxBox.TextColor3 = Color3.new(1,1,1)
maxBox.Font = Enum.Font.GothamBold
maxBox.TextSize = 14
maxBox.Parent = maxFrame
Instance.new("UICorner", maxBox).CornerRadius = UDim.new(0, 8)

local selectedModeLabel = Instance.new("TextLabel")
selectedModeLabel.Size = UDim2.new(0, 200, 1, 0)
selectedModeLabel.Position = UDim2.new(0, 125, 0, 0)
selectedModeLabel.BackgroundTransparency = 1
selectedModeLabel.Text = "Modo: Normal"
selectedModeLabel.TextColor3 = Color3.fromRGB(100, 255, 150)
selectedModeLabel.Font = Enum.Font.GothamBold
selectedModeLabel.TextSize = 13
selectedModeLabel.TextXAlignment = Enum.TextXAlignment.Left
selectedModeLabel.Parent = maxFrame

local modesFrame = Instance.new("Frame")
modesFrame.Size = UDim2.new(1, 0, 0, 78)
modesFrame.Position = UDim2.new(0, 0, 0, 60)
modesFrame.BackgroundTransparency = 1
modesFrame.Parent = salaContent

local modesGrid = Instance.new("UIGridLayout")
modesGrid.CellSize = UDim2.new(0.32, -4, 0, 32)
modesGrid.CellPadding = UDim2.new(0, 5, 0, 5)
modesGrid.HorizontalAlignment = Enum.HorizontalAlignment.Center
modesGrid.Parent = modesFrame

local modeBtns = {}
local modeColors = {
    Color3.fromRGB(50, 120, 200),
    Color3.fromRGB(60, 160, 100),
    Color3.fromRGB(80, 160, 200),
    Color3.fromRGB(140, 80, 180),
    Color3.fromRGB(180, 60, 60),
    Color3.fromRGB(200, 100, 40),
}

for i, mode in ipairs(gameModes) do
    local btn = Instance.new("TextButton")
    btn.BackgroundColor3 = Color3.fromRGB(40, 42, 58)
    btn.Text = mode.name
    btn.TextColor3 = Color3.fromRGB(200, 200, 210)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 11
    btn.Parent = modesFrame
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)

    btn.MouseButton1Click:Connect(function()
        selectedMode = mode.id
        selectedModeLabel.Text = "Modo: " .. mode.name
        for _, d in ipairs(modeBtns) do
            d.btn.BackgroundColor3 = Color3.fromRGB(40, 42, 58)
            d.btn.TextColor3 = Color3.fromRGB(200, 200, 210)
        end
        btn.BackgroundColor3 = modeColors[i]
        btn.TextColor3 = Color3.new(1,1,1)
    end)

    table.insert(modeBtns, {btn = btn, id = mode.id})
end

modeBtns[1].btn.BackgroundColor3 = modeColors[1]
modeBtns[1].btn.TextColor3 = Color3.new(1,1,1)

local salasTitle = Instance.new("TextLabel")
salasTitle.Size = UDim2.new(1, 0, 0, 22)
salasTitle.Position = UDim2.new(0, 0, 0, 145)
salasTitle.BackgroundTransparency = 1
salasTitle.Text = "Escolha a Sala (teleporta + cria party)"
salasTitle.TextColor3 = Color3.fromRGB(180, 220, 255)
salasTitle.Font = Enum.Font.GothamBold
salasTitle.TextSize = 13
salasTitle.Parent = salaContent

-- FRAME DOS BOTÕES DAS SALAS LADO A LADO
local salaButtonsFrame = Instance.new("Frame")
salaButtonsFrame.Size = UDim2.new(1, 0, 0, 52)
salaButtonsFrame.Position = UDim2.new(0, 0, 0, 168)
salaButtonsFrame.BackgroundTransparency = 1
salaButtonsFrame.Parent = salaContent

local salaGrid = Instance.new("UIGridLayout")
-- 4 botões um ao lado do outro, em tamanho compacto
salaGrid.CellSize = UDim2.new(0.235, -2, 0, 48)
salaGrid.CellPadding = UDim2.new(0, 4, 0, 4)
salaGrid.HorizontalAlignment = Enum.HorizontalAlignment.Center
salaGrid.Parent = salaButtonsFrame

local salaColors = {
    Color3.fromRGB(0, 140, 220),
    Color3.fromRGB(0, 170, 120),
    Color3.fromRGB(200, 120, 40),
    Color3.fromRGB(160, 60, 200),
}

for i, data in ipairs(salas) do
    local btn = Instance.new("TextButton")
    btn.BackgroundColor3 = salaColors[i]
    btn.Text = data.name .. "\nCriar"
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 11
    btn.TextWrapped = true
    btn.Parent = salaButtonsFrame
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)

    local strokeBtn = Instance.new("UIStroke", btn)
    strokeBtn.Color = Color3.fromRGB(255, 255, 255)
    strokeBtn.Thickness = 1
    strokeBtn.Transparency = 0.5

    btn.MouseButton1Click:Connect(function()
        local maxM = tonumber(maxBox.Text) or 1
        maxM = math.clamp(maxM, 1, 10)

        TeleportToSala(data.pos)
        task.wait(0.35)
        CreateParty(maxM, "default", selectedMode, false)

        game:GetService("StarterGui"):SetCore("SendNotification", {
            Title = data.name,
            Text = "Teleportado + Party (" .. selectedMode .. " | max " .. maxM .. ")",
            Duration = 3
        })
    end)
end

local quickSolo = Instance.new("TextButton")
quickSolo.Size = UDim2.new(1, 0, 0, 36)
quickSolo.Position = UDim2.new(0, 0, 0, 230)
quickSolo.BackgroundColor3 = Color3.fromRGB(0, 100, 180)
quickSolo.Text = "⚡ SALA 1 SOLO RÁPIDO (max 1)"
quickSolo.TextColor3 = Color3.new(1,1,1)
quickSolo.Font = Enum.Font.GothamBold
quickSolo.TextSize = 13
quickSolo.Parent = salaContent
Instance.new("UICorner", quickSolo).CornerRadius = UDim.new(0, 10)

quickSolo.MouseButton1Click:Connect(function()
    TeleportToSala(salas[1].pos)
    task.wait(0.3)
    CreateParty(1, "default", selectedMode, false)
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Sala Solo",
        Text = "Sala 1 + " .. selectedMode .. " (max 1)",
        Duration = 3
    })
end)

-------------------------------------------------
-- TROCA DE ABAS
-------------------------------------------------
local function setTab(active, ...)
    local args = {...}
    active.BackgroundColor3 = Color3.fromRGB(0, 130, 220)
    active.TextColor3 = Color3.new(1,1,1)
    for i = 1, #args, 2 do
        local inactive = args[i]
        local content = args[i+1]
        inactive.BackgroundColor3 = Color3.fromRGB(45, 47, 65)
        inactive.TextColor3 = Color3.fromRGB(180, 180, 190)
        content.Visible = false
    end
end

classTab.MouseButton1Click:Connect(function()
    setTab(classTab, trainTab, trainContent, shopTab, shopContent, salaTab, salaContent)
    classContent.Visible = true
end)
trainTab.MouseButton1Click:Connect(function()
    setTab(trainTab, classTab, classContent, shopTab, shopContent, salaTab, salaContent)
    trainContent.Visible = true
end)
shopTab.MouseButton1Click:Connect(function()
    setTab(shopTab, classTab, classContent, trainTab, trainContent, salaTab, salaContent)
    shopContent.Visible = true
end)
salaTab.MouseButton1Click:Connect(function()
    setTab(salaTab, classTab, classContent, trainTab, trainContent, shopTab, shopContent)
    salaContent.Visible = true
end)

classTab.BackgroundColor3 = Color3.fromRGB(0, 130, 220)
classTab.TextColor3 = Color3.new(1,1,1)

print("✅ Script atualizado - UI rebaixada e botões de sala lado a lado em tamanho compacto!")
