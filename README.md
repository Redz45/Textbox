-- LocalScript em StarterGui

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local TextChatService = game:GetService("TextChatService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ChatGui"
screenGui.ResetOnSpawn = false
screenGui.Parent = playerGui

-- Função para tornar um UI objeto arrastável, incluindo seus filhos
local function makeDraggable(uiObject)
    local dragging = false
    local dragInput, dragStart, startPos

    local function onInputBegan(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = uiObject.Position

            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end

    local function onInputChanged(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end

    uiObject.InputBegan:Connect(onInputBegan)
    uiObject.InputChanged:Connect(onInputChanged)

    for _, child in ipairs(uiObject:GetDescendants()) do
        if child:IsA("GuiObject") then
            child.InputBegan:Connect(onInputBegan)
            child.InputChanged:Connect(onInputChanged)
        end
    end

    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            uiObject.Position = UDim2.new(
                startPos.X.Scale, startPos.X.Offset + delta.X,
                startPos.Y.Scale, startPos.Y.Offset + delta.Y
            )
        end
    end)
end

-- FRAME PRINCIPAL
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 300, 0, 130)
mainFrame.Position = UDim2.new(0.5, -150, 0.2, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui

-- Arredondar frame principal
local mainCorner = Instance.new("UICorner")
mainCorner.CornerRadius = UDim.new(0, 10)
mainCorner.Parent = mainFrame

-- Caixa de texto
local textBox = Instance.new("TextBox")
textBox.Name = "MessageBox"
textBox.Size = UDim2.new(1, -20, 0, 50)
textBox.Position = UDim2.new(0, 10, 0, 50)
textBox.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
textBox.TextColor3 = Color3.new(1,1,1)
textBox.ClearTextOnFocus = false
textBox.PlaceholderText = "Cole ou digite sua mensagem aqui..."
textBox.Parent = mainFrame

-- Arredondar caixa de texto
local textCorner = Instance.new("UICorner")
textCorner.CornerRadius = UDim.new(0, 8)
textCorner.Parent = textBox

-- Label acima da caixa de texto
local titleLabel = Instance.new("TextLabel")
titleLabel.Text = "textbox toddy v2"
titleLabel.Size = UDim2.new(1, -20, 0, 20)
titleLabel.Position = UDim2.new(0, 10, 0, 28)
titleLabel.BackgroundTransparency = 1
titleLabel.TextColor3 = Color3.fromRGB(200,200,200)
titleLabel.TextScaled = true
titleLabel.Font = Enum.Font.SourceSansBold
titleLabel.TextXAlignment = Enum.TextXAlignment.Left
titleLabel.Parent = mainFrame

-- Botões no canto (minimizar e fechar)
local minimizeButton = Instance.new("TextButton")
minimizeButton.Size = UDim2.new(0, 26, 0, 26)
minimizeButton.Position = UDim2.new(1, -60, 0, 8)
minimizeButton.Text = "-"
minimizeButton.BackgroundColor3 = Color3.fromRGB(100,100,100)
minimizeButton.TextColor3 = Color3.new(1,1,1)
minimizeButton.Parent = mainFrame
Instance.new("UICorner", minimizeButton).CornerRadius = UDim.new(0,6)

local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 26, 0, 26)
closeButton.Position = UDim2.new(1, -30, 0, 8)
closeButton.Text = "×"
closeButton.BackgroundColor3 = Color3.fromRGB(150,50,50)
closeButton.TextColor3 = Color3.new(1,1,1)
closeButton.Parent = mainFrame
Instance.new("UICorner", closeButton).CornerRadius = UDim.new(0,6)

makeDraggable(mainFrame)

-- MINI FRAME (quando minimizado) - contém Enviar e +
local miniFrame = Instance.new("Frame")
miniFrame.Name = "MiniFrame"
miniFrame.Size = UDim2.new(0, 120, 0, 48)
miniFrame.Position = UDim2.new(0.5, -60, 0, 20)
miniFrame.BackgroundColor3 = Color3.fromRGB(50,50,50)
miniFrame.BorderSizePixel = 0
miniFrame.Visible = false
miniFrame.Parent = screenGui
Instance.new("UICorner", miniFrame).CornerRadius = UDim.new(0, 10)

-- Botão Enviar (dentro do miniFrame)
local miniSend = Instance.new("TextButton")
miniSend.Name = "MiniSend"
miniSend.Size = UDim2.new(0.7, -2, 1, 0)
miniSend.Position = UDim2.new(0, 0, 0, 0)
miniSend.Text = "Enviar"
miniSend.BackgroundColor3 = Color3.fromRGB(50,150,50)
miniSend.TextColor3 = Color3.new(1,1,1)
miniSend.Parent = miniFrame
Instance.new("UICorner", miniSend).CornerRadius = UDim.new(0,8)

-- Botão "+" para reabrir a UI
local reopenButton = Instance.new("TextButton")
reopenButton.Name = "Reopen"
reopenButton.Size = UDim2.new(0.3, 0, 1, 0)
reopenButton.Position = UDim2.new(0.7, 0, 0, 0)
reopenButton.Text = "+"
reopenButton.BackgroundColor3 = Color3.fromRGB(80,80,200)
reopenButton.TextColor3 = Color3.new(1,1,1)
reopenButton.Parent = miniFrame
Instance.new("UICorner", reopenButton).CornerRadius = UDim.new(0,8)

makeDraggable(miniFrame)

-- FUNÇÃO ATUALIZADA PARA ENVIAR MENSAGEM
local function sendMessage(msg)
    if not msg or msg:match("^%s*$") then return end

    -- Tenta o chat novo primeiro
    local success = false
    pcall(function()
        local channel = TextChatService.ChatInputBarConfiguration.TargetTextChannel
        if channel then
            channel:SendAsync(msg)
            success = true
        end
    end)

    -- Se não funcionou, tenta o chat antigo
    if not success then
        local chatEvent = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents")
        if chatEvent and chatEvent:FindFirstChild("SayMessageRequest") then
            chatEvent.SayMessageRequest:FireServer(msg, "All")
        else
            warn("Nenhum sistema de chat encontrado para enviar mensagem.")
        end
    end
end

-- EVENTOS
minimizeButton.MouseButton1Click:Connect(function()
    mainFrame.Visible = false
    miniFrame.Visible = true
end)

closeButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

miniSend.MouseButton1Click:Connect(function()
    sendMessage(textBox.Text)
end)

reopenButton.MouseButton1Click:Connect(function()
    mainFrame.Visible = true
    miniFrame.Visible = false
end)
