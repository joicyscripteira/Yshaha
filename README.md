-- ESP com botão para celular, texto vermelho e funcional
-- Coloque em StarterPlayerScripts (LocalScript)

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local espEnabled = true
local createdESPs = {}

-- Cria a interface de botão para celular
local function createToggleButton()
	local screenGui = Instance.new("ScreenGui")
	screenGui.Name = "ESP_UI"
	screenGui.ResetOnSpawn = false
	screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

	local button = Instance.new("TextButton")
	button.Name = "ToggleESP"
	button.Size = UDim2.new(0, 120, 0, 40)
	button.Position = UDim2.new(0, 20, 0, 80)
	button.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
	button.TextColor3 = Color3.fromRGB(255, 255, 255)
	button.Text = "ESP: ON"
	button.Font = Enum.Font.GothamBold
	button.TextScaled = true
	button.Parent = screenGui

	button.MouseButton1Click:Connect(function()
		espEnabled = not espEnabled
		button.Text = espEnabled and "ESP: ON" or "ESP: OFF"
		updateAllESP()
	end)
end

-- Cria o ESP em cima da cabeça do inimigo
local function createESP(player)
	if player == LocalPlayer then return end
	if player.Team == LocalPlayer.Team then return end

	local character = player.Character
	if not character or not character:FindFirstChild("Head") then return end
	local head = character:FindFirstChild("Head")

	-- Se já existe um ESP, evita recriar
	if head:FindFirstChild("ESPGui") then return end

	local billboard = Instance.new("BillboardGui")
	billboard.Name = "ESPGui"
	billboard.Adornee = head
	billboard.Size = UDim2.new(0, 100, 0, 30)
	billboard.StudsOffset = Vector3.new(0, 2, 0)
	billboard.AlwaysOnTop = true

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, 0, 1, 0)
	label.BackgroundTransparency = 1
	label.Text = player.Name
	label.TextColor3 = Color3.fromRGB(255, 0, 0) -- VERMELHO
	label.TextStrokeTransparency = 0.5
	label.TextScaled = true
	label.Font = Enum.Font.GothamBold
	label.Parent = billboard

	billboard.Parent = head

	-- Guarda referência para ativar/desativar depois
	createdESPs[player] = billboard
end

-- Remove o ESP de um jogador
local function removeESP(player)
	if createdESPs[player] then
		createdESPs[player]:Destroy()
		createdESPs[player] = nil
	end
end

-- Atualiza todos os ESPs de acordo com o estado atual
function updateAllESP()
	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer then
			if espEnabled then
				createESP(player)
			else
				removeESP(player)
			end
		end
	end
end

-- Conecta eventos para cada jogador
local function onPlayerAdded(player)
	player.CharacterAdded
 
