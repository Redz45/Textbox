-- LocalScript em StarterGui

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local TextChatService = game:GetService("TextChatService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Lista de jogadores autorizados
local allowedPlayers = {
    ["hprato"] = true,
    ["vyyghjgffggh"] = true,
    ["iheibus"] = true,
    ["mvkmuitafe7"] = true, -- ✅ novo jogador adicionado
}

-- Se o jogador não estiver na lista, a GUI não aparece
if not allowedPlayers[player.Name] then
    return
end

-- ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ChatGui"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.Parent = playerGui

-- Função para tornar UI arrastável
local function makeDraggable(uiObject)
    local dragging = false
    local dragInput, dragStart, startPos

    local function onInputBegan(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
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
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
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
mainFrame.Size = UDim2.new(0, 300, 0, 200)
mainFrame.Position = UDim2.new(0.5, -150, 0.2, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui
Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 10)

-- Caixa de texto principal
local textBox = Instance.new("TextBox")
textBox.Name = "MessageBox"
textBox.Size = UDim2.new(1, -20, 0, 50)
textBox.Position = UDim2.new(0, 10, 0, 50)
textBox.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
textBox.TextColor3 = Color3.new(1,1,1)
textBox.ClearTextOnFocus = false
textBox.PlaceholderText = "Cole ou digite sua mensagem aqui..."
textBox.Parent = mainFrame
Instance.new("UICorner", textBox).CornerRadius = UDim.new(0, 8)

-- Label acima da caixa de texto
local titleLabel = Instance.new("TextLabel")
titleLabel.Text = "textbox toddy v4"
titleLabel.Size = UDim2.new(1, -20, 0, 20)
titleLabel.Position = UDim2.new(0, 10, 0, 28)
titleLabel.BackgroundTransparency = 1
titleLabel.TextColor3 = Color3.fromRGB(200,200,200)
titleLabel.TextScaled = true
titleLabel.Font = Enum.Font.SourceSansBold
titleLabel.TextXAlignment = Enum.TextXAlignment.Left
titleLabel.Parent = mainFrame

-- Botões minimizar e fechar
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

-- Botões Mat e Kit
local matButton = Instance.new("TextButton")
matButton.Size = UDim2.new(0.5, -15, 0, 30)
matButton.Position = UDim2.new(0, 10, 0, 110)
matButton.Text = "Mat"
matButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
matButton.TextColor3 = Color3.new(1,1,1)
matButton.Parent = mainFrame
Instance.new("UICorner", matButton).CornerRadius = UDim.new(0,8)

local kitButton = Instance.new("TextButton")
kitButton.Size = UDim2.new(0.5, -15, 0, 30)
kitButton.Position = UDim2.new(0.5, 5, 0, 110)
kitButton.Text = "Kit"
kitButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
kitButton.TextColor3 = Color3.new(1,1,1)
kitButton.Parent = mainFrame
Instance.new("UICorner", kitButton).CornerRadius = UDim.new(0,8)

-- Quadradinho para tecla de atalho
local keyLabel = Instance.new("TextLabel")
keyLabel.Text = "Atalho:"
keyLabel.Size = UDim2.new(0, 60, 0, 20)
keyLabel.Position = UDim2.new(0, 10, 0, 150)
keyLabel.BackgroundTransparency = 1
keyLabel.TextColor3 = Color3.fromRGB(200,200,200)
keyLabel.TextScaled = true
keyLabel.Font = Enum.Font.SourceSansBold
keyLabel.Parent = mainFrame

local keyBox = Instance.new("TextBox")
keyBox.Size = UDim2.new(0, 30, 0, 30)
keyBox.Position = UDim2.new(0, 75, 0, 146)
keyBox.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
keyBox.TextColor3 = Color3.new(1,1,1)
keyBox.PlaceholderText = "-"
keyBox.TextScaled = true
keyBox.ClearTextOnFocus = true
keyBox.MaxVisibleGraphemes = 1
keyBox.Parent = mainFrame
Instance.new("UICorner", keyBox).CornerRadius = UDim.new(0, 6)

-- MINI FRAME
local miniFrame = Instance.new("Frame")
miniFrame.Name = "MiniFrame"
miniFrame.Size = UDim2.new(0, 120, 0, 48)
miniFrame.Position = UDim2.new(0.5, -60, 0, 20)
miniFrame.BackgroundColor3 = Color3.fromRGB(50,50,50)
miniFrame.BorderSizePixel = 0
miniFrame.Visible = false
miniFrame.Parent = screenGui
Instance.new("UICorner", miniFrame).CornerRadius = UDim.new(0, 10)

local miniSend = Instance.new("TextButton")
miniSend.Name = "MiniSend"
miniSend.Size = UDim2.new(0.7, -2, 1, 0)
miniSend.Position = UDim2.new(0, 0, 0, 0)
miniSend.Text = "Enviar"
miniSend.BackgroundColor3 = Color3.fromRGB(50,150,50)
miniSend.TextColor3 = Color3.new(1,1,1)
miniSend.Parent = miniFrame
Instance.new("UICorner", miniSend).CornerRadius = UDim.new(0,8)

local reopenButton = Instance.new("TextButton")
reopenButton.Name = "Reopen"
reopenButton.Size = UDim2.new(0.3, 0, 1, 0)
reopenButton.Position = UDim2.new(0.7, 0, 0, 0)
reopenButton.Text = "+"
reopenButton.BackgroundColor3 = Color3.fromRGB(80,80,200)
reopenButton.TextColor3 = Color3.new(1,1,1)
reopenButton.Parent = miniFrame
Instance.new("UICorner", reopenButton).CornerRadius = UDim.new(0,8)

makeDraggable(mainFrame)
makeDraggable(miniFrame)

-- Guardar tecla escolhida
local selectedKey = nil
keyBox.FocusLost:Connect(function()
    local key = keyBox.Text:upper():sub(1,1)
    if key ~= "" then
        selectedKey = key
        keyBox.Text = key
    else
        selectedKey = nil
        keyBox.Text = "-"
    end
end)

-- Contador automático de mensagens
local messageCount = 0
local minSpaces = 1
local maxSpaces = 10

local function gerarMensagemPontos()
	local spaceCount = math.random(minSpaces, maxSpaces)
	local spaces = string.rep(" ", spaceCount)
	return "." .. spaces .. "."
end

-- Função de enviar mensagem
local function sendMessage(msg)
	if not msg or msg:match("^%s*$") then return end
	local success = false
	pcall(function()
		local channel = TextChatService.ChatInputBarConfiguration.TargetTextChannel
		if channel then
			channel:SendAsync(msg)
			success = true
		end
	end)
	if not success then
		local chatEvent = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents")
		if chatEvent and chatEvent:FindFirstChild("SayMessageRequest") then
			chatEvent.SayMessageRequest:FireServer(msg, "All")
		end
	end

	-- Incrementa contador e envia automaticamente ".   ."
	messageCount = messageCount + 1
	if messageCount >= 5 then
		messageCount = 0
		local autoMsg = gerarMensagemPontos()
		pcall(function()
			local channel = TextChatService.ChatInputBarConfiguration.TargetTextChannel
			if channel then
				channel:SendAsync(autoMsg)
			else
				local chatEvent = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents")
				if chatEvent and chatEvent:FindFirstChild("SayMessageRequest") then
					chatEvent.SayMessageRequest:FireServer(autoMsg, "All")
				end
			end
		end)
	end
end

-- Eventos dos botões
matButton.MouseButton1Click:Connect(function()
	textBox.Text = "[×] matar [×] tiro estoura blindado"
end)

kitButton.MouseButton1Click:Connect(function()
	textBox.Text = "[+] kit médico [+] bandagem"
end)

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

-- Tecla física para enviar mensagem
UserInputService.InputBegan:Connect(function(input, processed)
	if processed then return end
	if input.UserInputType == Enum.UserInputType.Keyboard and selectedKey then
		if input.KeyCode.Name:upper() == selectedKey then
			sendMessage(textBox.Text)
		end
	end
end)
