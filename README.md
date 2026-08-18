--// TRAINING AIM SIMULATOR
--// Roblox Studio - NPC/Dummy Training Only
--// Coloque este LocalScript em:
--// StarterPlayer > StarterPlayerScripts
--
--// Seus NPCs de treinamento devem ficar em:
--// workspace.TrainingNPCs

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")

local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

--==================================================
-- CONFIGURAÇÃO
--==================================================

local CONFIG = {
	AimEnabled = false,
	AimDistance = 200,
	AimFOV = 120,
	AimSmoothness = 0.18,

	ESPEnabled = false,
	ESPDistance = 1000,

	SkeletonEnabled = false,

	NoclipEnabled = false,

	PanelOpen = true,

	-- Apenas NPCs dentro desta pasta
	NPCFolderName = "TrainingNPCs",
}

--==================================================
-- CORES
--==================================================

local NEON_PINK = Color3.fromRGB(255, 20, 180)
local HOT_PINK = Color3.fromRGB(255, 0, 130)
local PURPLE = Color3.fromRGB(125, 20, 180)
local DARK = Color3.fromRGB(15, 8, 20)
local DARK2 = Color3.fromRGB(25, 12, 32)
local WHITE = Color3.fromRGB(255, 255, 255)
local GREEN = Color3.fromRGB(80, 255, 150)
local RED = Color3.fromRGB(255, 70, 90)

--==================================================
-- UTILIDADES
--==================================================

local function getNPCFolder()
	return Workspace:FindFirstChild(CONFIG.NPCFolderName)
end

local function getHumanoid(model)
	if not model then
		return nil
	end

	return model:FindFirstChildOfClass("Humanoid")
end

local function getHead(model)
	if not model then
		return nil
	end

	return model:FindFirstChild("Head")
end

local function getRoot(model)
	if not model then
		return nil
	end

	return model:FindFirstChild("HumanoidRootPart")
		or model.PrimaryPart
end

local function getDistance(model)
	local character = LocalPlayer.Character
	local root = character and getRoot(character)
	local targetRoot = getRoot(model)

	if not root or not targetRoot then
		return math.huge
	end

	return (root.Position - targetRoot.Position).Magnitude
end

local function isValidNPC(model)
	if not model or not model:IsA("Model") then
		return false
	end

	local humanoid = getHumanoid(model)
	local head = getHead(model)

	if not humanoid or not head then
		return false
	end

	if humanoid.Health <= 0 then
		return false
	end

	return true
end

local function getNPCs()
	local folder = getNPCFolder()
	if not folder then
		return {}
	end

	local result = {}

	for _, object in ipairs(folder:GetChildren()) do
		if isValidNPC(object) then
			table.insert(result, object)
		end
	end

	return result
end

--==================================================
-- GUI PRINCIPAL
--==================================================

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "TrainingAimSimulator"
ScreenGui.ResetOnSpawn = false
ScreenGui.IgnoreGuiInset = true
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

--==================================================
-- FOV
--==================================================

local FOVCircle = Instance.new("Frame")
FOVCircle.Name = "FOVCircle"
FOVCircle.AnchorPoint = Vector2.new(0.5, 0.5)
FOVCircle.Position = UDim2.fromScale(0.5, 0.5)
FOVCircle.Size = UDim2.fromOffset(CONFIG.AimFOV * 2, CONFIG.AimFOV * 2)
FOVCircle.BackgroundTransparency = 1
FOVCircle.BorderSizePixel = 0
FOVCircle.Visible = true
FOVCircle.ZIndex = 2
FOVCircle.Parent = ScreenGui

local FOVCorner = Instance.new("UICorner")
FOVCorner.CornerRadius = UDim.new(1, 0)
FOVCorner.Parent = FOVCircle

local FOVStroke = Instance.new("UIStroke")
FOVStroke.Color = NEON_PINK
FOVStroke.Thickness = 2
FOVStroke.Transparency = 0.15
FOVStroke.Parent = FOVCircle

--==================================================
-- PAINEL
--==================================================

local Main = Instance.new("Frame")
Main.Name = "Main"
Main.Size = UDim2.fromOffset(420, 560)
Main.Position = UDim2.new(0.5, -210, 0.5, -280)
Main.BackgroundColor3 = DARK
Main.BorderSizePixel = 0
Main.ZIndex = 10
Main.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 14)
MainCorner.Parent = Main

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = NEON_PINK
MainStroke.Thickness = 2
MainStroke.Parent = Main

--==================================================
-- HEADER
--==================================================

local Header = Instance.new("Frame")
Header.Size = UDim2.new(1, 0, 0, 70)
Header.BackgroundColor3 = DARK2
Header.BorderSizePixel = 0
Header.ZIndex = 11
Header.Parent = Main

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 14)
HeaderCorner.Parent = Header

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -25, 0, 30)
Title.Position = UDim2.fromOffset(15, 8)
Title.BackgroundTransparency = 1
Title.Text = "NEON AIM SIMULATOR"
Title.TextColor3 = NEON_PINK
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.ZIndex = 12
Title.Parent = Header

local Subtitle = Instance.new("TextLabel")
Subtitle.Size = UDim2.new(1, -25, 0, 20)
Subtitle.Position = UDim2.fromOffset(15, 38)
Subtitle.BackgroundTransparency = 1
Subtitle.Text = "NPC TRAINING MODE • RightShift"
Subtitle.TextColor3 = Color3.fromRGB(190, 150, 200)
Subtitle.Font = Enum.Font.Gotham
Subtitle.TextSize = 12
Subtitle.TextXAlignment = Enum.TextXAlignment.Left
Subtitle.ZIndex = 12
Subtitle.Parent = Header

--==================================================
-- CONTAINER
--==================================================

local Content = Instance.new("ScrollingFrame")
Content.Name = "Content"
Content.Size = UDim2.new(1, -20, 1, -85)
Content.Position = UDim2.fromOffset(10, 78)
Content.BackgroundTransparency = 1
Content.BorderSizePixel = 0
Content.ScrollBarThickness = 4
Content.ScrollBarImageColor3 = NEON_PINK
Content.CanvasSize = UDim2.new(0, 0, 0, 700)
Content.ZIndex = 11
Content.Parent = Main

local Layout = Instance.new("UIListLayout")
Layout.Padding = UDim.new(0, 8)
Layout.SortOrder = Enum.SortOrder.LayoutOrder
Layout.Parent = Content

--==================================================
-- FUNÇÕES DE GUI
--==================================================

local function createSection(text)
	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, -10, 0, 30)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = NEON_PINK
	label.Font = Enum.Font.GothamBold
	label.TextSize = 15
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.ZIndex = 12
	label.Parent = Content

	return label
end

local function createToggle(text, default, callback)
	local button = Instance.new("TextButton")
	button.Size = UDim2.new(1, -10, 0, 42)
	button.BackgroundColor3 = DARK2
	button.BorderSizePixel = 0
	button.AutoButtonColor = false
	button.Text = ""
	button.ZIndex = 12
	button.Parent = Content

	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, 8)
	corner.Parent = button

	local stroke = Instance.new("UIStroke")
	stroke.Color = Color3.fromRGB(70, 30, 80)
	stroke.Thickness = 1
	stroke.Parent = button

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, -75, 1, 0)
	label.Position = UDim2.fromOffset(15, 0)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = WHITE
	label.Font = Enum.Font.GothamMedium
	label.TextSize = 14
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.ZIndex = 13
	label.Parent = button

	local status = Instance.new("TextLabel")
	status.Size = UDim2.fromOffset(50, 25)
	status.Position = UDim2.new(1, -60, 0.5, -12)
	status.BackgroundColor3 = Color3.fromRGB(55, 25, 60)
	status.TextColor3 = Color3.fromRGB(180, 150, 180)
	status.Text = "OFF"
	status.Font = Enum.Font.GothamBold
	status.TextSize = 11
	status.ZIndex = 13
	status.Parent = button

	local statusCorner = Instance.new("UICorner")
	statusCorner.CornerRadius = UDim.new(0, 6)
	statusCorner.Parent = status

	local enabled = default

	local function update()
		if enabled then
			status.Text = "ON"
			status.BackgroundColor3 = NEON_PINK
			status.TextColor3 = WHITE
		else
			status.Text = "OFF"
			status.BackgroundColor3 = Color3.fromRGB(55, 25, 60)
			status.TextColor3 = Color3.fromRGB(180, 150, 180)
		end

		callback(enabled)
	end

	button.MouseButton1Click:Connect(function()
		enabled = not enabled
		update()
	end)

	update()

	return button
end

local function createSlider(text, minValue, maxValue, defaultValue, callback)
	local holder = Instance.new("Frame")
	holder.Size = UDim2.new(1, -10, 0, 70)
	holder.BackgroundColor3 = DARK2
	holder.BorderSizePixel = 0
	holder.ZIndex = 12
	holder.Parent = Content

	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, 8)
	corner.Parent = holder

	local title = Instance.new("TextLabel")
	title.Size = UDim2.new(1, -30, 0, 25)
	title.Position = UDim2.fromOffset(15, 5)
	title.BackgroundTransparency = 1
	title.TextColor3 = WHITE
	title.Font = Enum.Font.GothamMedium
	title.TextSize = 13
	title.TextXAlignment = Enum.TextXAlignment.Left
	title.Text = text
	title.ZIndex = 13
	title.Parent = holder

	local valueLabel = Instance.new("TextLabel")
	valueLabel.Size = UDim2.fromOffset(80, 25)
	valueLabel.Position = UDim2.new(1, -90, 0, 5)
	valueLabel.BackgroundTransparency = 1
	valueLabel.TextColor3 = NEON_PINK
	valueLabel.Font = Enum.Font.GothamBold
	valueLabel.TextSize = 13
	valueLabel.TextXAlignment = Enum.TextXAlignment.Right
	valueLabel.ZIndex = 13
	valueLabel.Parent = holder

	local bar = Instance.new("Frame")
	bar.Size = UDim2.new(1, -30, 0, 7)
	bar.Position = UDim2.fromOffset(15, 43)
	bar.BackgroundColor3 = Color3.fromRGB(55, 25, 60)
	bar.BorderSizePixel = 0
	bar.ZIndex = 13
	bar.Parent = holder

	local barCorner = Instance.new("UICorner")
	barCorner.CornerRadius = UDim.new(1, 0)
	barCorner.Parent = bar

	local fill = Instance.new("Frame")
	fill.Size = UDim2.new((defaultValue - minValue) / (maxValue - minValue), 0, 1, 0)
	fill.BackgroundColor3 = NEON_PINK
	fill.BorderSizePixel = 0
	fill.ZIndex = 14
	fill.Parent = bar

	local fillCorner = Instance.new("UICorner")
	fillCorner.CornerRadius = UDim.new(1, 0)
	fillCorner.Parent = fill

	local dragging = false
	local currentValue = defaultValue

	local function setValue(value)
		value = math.clamp(value, minValue, maxValue)
		value = math.floor(value)

		currentValue = value

		local percentage =
			(value - minValue) /
			(maxValue - minValue)

		fill.Size = UDim2.new(percentage, 0, 1, 0)
		valueLabel.Text = tostring(value)

		callback(value)
	end

	local function updateFromMouse(x)
		local startX = bar.AbsolutePosition.X
		local width = bar.AbsoluteSize.X

		local percentage = math.clamp(
			(x - startX) / width,
			0,
			1
		)

		setValue(
			minValue +
			(maxValue - minValue) * percentage
		)
	end

	bar.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			updateFromMouse(input.Position.X)
		end
	end)

	UserInputService.InputChanged:Connect(function(input)
		if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
			updateFromMouse(input.Position.X)
		end
	end)

	UserInputService.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = false
		end
	end)

	setValue(defaultValue)

	return holder
end

--==================================================
-- AIM
--==================================================

createSection("🎯 AIM ASSIST")

createToggle(
	"Aim Assist • NPC Head",
	false,
	function(value)
		CONFIG.AimEnabled = value
	end
)

createSlider(
	"Aim Distance",
	1,
	200,
	200,
	function(value)
		CONFIG.AimDistance = value
	end
)

createSlider(
	"Aim FOV",
	1,
	500,
	120,
	function(value)
		CONFIG.AimFOV = value
		FOVCircle.Size = UDim2.fromOffset(value * 2, value * 2)
	end
)

createSlider(
	"Aim Smoothness",
	1,
	100,
	18,
	function(value)
		CONFIG.AimSmoothness = value / 100
	end
)

--==================================================
-- ESP
--==================================================

createSection("👁 ESP / ALL CHECK")

createToggle(
	"ESP",
	false,
	function(value)
		CONFIG.ESPEnabled = value
	end
)

createToggle(
	"Skeleton",
	false,
	function(value)
		CONFIG.SkeletonEnabled = value
	end
)

createSlider(
	"ESP Distance",
	1,
	3000,
	1000,
	function(value)
		CONFIG.ESPDistance = value
	end
)

--==================================================
-- MOVIMENTO
--==================================================

createSection("🌀 MOVEMENT")

createToggle(
	"Noclip",
	false,
	function(value)
		CONFIG.NoclipEnabled = value
	end
)

--==================================================
-- ESP SYSTEM
--==================================================

local ESPObjects = {}

local function destroyESP(model)
	local data = ESPObjects[model]

	if data then
		if data.billboard then
			data.billboard:Destroy()
		end

		if data.highlight then
			data.highlight:Destroy()
		end

		if data.skeletonFolder then
			data.skeletonFolder:Destroy()
		end

		ESPObjects[model] = nil
	end
end

local function createESP(model)
	if ESPObjects[model] then
		return ESPObjects[model]
	end

	local head = getHead(model)
	if not head then
		return
	end

	local billboard = Instance.new("BillboardGui")
	billboard.Name = "TrainingESP"
	billboard.Adornee = head
	billboard.Size = UDim2.fromOffset(180, 70)
	billboard.StudsOffset = Vector3.new(0, 2.8, 0)
	billboard.AlwaysOnTop = true
	billboard.Enabled = false
	billboard.Parent = ScreenGui

	local nameLabel = Instance.new("TextLabel")
	nameLabel.Size = UDim2.new(1, 0, 0, 22)
	nameLabel.BackgroundTransparency = 1
	nameLabel.TextColor3 = NEON_PINK
	nameLabel.TextStrokeTransparency = 0.2
	nameLabel.Font = Enum.Font.GothamBold
	nameLabel.TextSize = 14
	nameLabel.Parent = billboard

	local distanceLabel = Instance.new("TextLabel")
	distanceLabel.Size = UDim2.new(1, 0, 0, 18)
	distanceLabel.Position = UDim2.fromOffset(0, 20)
	distanceLabel.BackgroundTransparency = 1
	distanceLabel.TextColor3 = WHITE
	distanceLabel.TextStrokeTransparency = 0.2
	distanceLabel.Font = Enum.Font.Gotham
	distanceLabel.TextSize = 11
	distanceLabel.Parent = billboard

	local healthBackground = Instance.new("Frame")
	healthBackground.Size = UDim2.new(0.75, 0, 0, 6)
	healthBackground.Position = UDim2.new(0.125, 0, 0, 43)
	healthBackground.BackgroundColor3 = Color3.fromRGB(45, 20, 50)
	healthBackground.BorderSizePixel = 0
	healthBackground.Parent = billboard

	local healthCorner = Instance.new("UICorner")
	healthCorner.CornerRadius = UDim.new(1, 0)
	healthCorner.Parent = healthBackground

	local healthBar = Instance.new("Frame")
	healthBar.Size = UDim2.new(1, 0, 1, 0)
	healthBar.BackgroundColor3 = GREEN
	healthBar.BorderSizePixel = 0
	healthBar.Parent = healthBackground

	local healthBarCorner = Instance.new("UICorner")
	healthBarCorner.CornerRadius = UDim.new(1, 0)
	healthBarCorner.Parent = healthBar

	local highlight = Instance.new("Highlight")
	highlight.Name = "TrainingHighlight"
	highlight.Adornee = model
	highlight.FillColor = PURPLE
	highlight.OutlineColor = NEON_PINK
	highlight.FillTransparency = 0.8
	highlight.OutlineTransparency = 0.1
	highlight.Enabled = false
	highlight.Parent = model

	local skeletonFolder = Instance.new("Folder")
	skeletonFolder.Name = "TrainingSkeleton"
	skeletonFolder.Parent = model

	ESPObjects[model] = {
		billboard = billboard,
		nameLabel = nameLabel,
		distanceLabel = distanceLabel,
		healthBar = healthBar,
		highlight = highlight,
		skeletonFolder = skeletonFolder,
	}

	return ESPObjects[model]
end

--==================================================
-- SKELETON
--==================================================

local skeletonConnections = {
	{"Head", "UpperTorso"},
	{"UpperTorso", "LowerTorso"},

	{"UpperTorso", "LeftUpperArm"},
	{"LeftUpperArm", "LeftLowerArm"},
	{"LeftLowerArm", "LeftHand"},

	{"UpperTorso", "RightUpperArm"},
	{"RightUpperArm", "RightLowerArm"},
	{"RightLowerArm", "RightHand"},

	{"LowerTorso", "LeftUpperLeg"},
	{"LeftUpperLeg", "LeftLowerLeg"},
	{"LeftLowerLeg", "LeftFoot"},

	{"LowerTorso", "RightUpperLeg"},
	{"RightUpperLeg", "RightLowerLeg"},
	{"RightLowerLeg", "RightFoot"},
}

local function createSkeletonPart(folder, partA, partB)
	if not partA or not partB then
		return
	end

	local midpoint =
		(partA.Position + partB.Position) / 2

	local distance =
		(partA.Position - partB.Position).Magnitude

	local beam = Instance.new("Part")
	beam.Name = "Bone"
	beam.Anchored = true
	beam.CanCollide = false
	beam.CanTouch = false
	beam.CanQuery = false
	beam.Material = Enum.Material.Neon
	beam.Color = NEON_PINK
	beam.Size = Vector3.new(0.045, 0.045, distance)
	beam.CFrame =
		CFrame.lookAt(midpoint, partB.Position)

	beam.Parent = folder
end

local function updateSkeleton(model, data)
	if not data then
		return
	end

	local folder = data.skeletonFolder

	for _, child in ipairs(folder:GetChildren()) do
		child:Destroy()
	end

	if not CONFIG.SkeletonEnabled then
		return
	end

	if not CONFIG.ESPEnabled then
		return
	end

	local distance = getDistance(model)

	if distance > CONFIG.ESPDistance then
		return
	end

	for _, connection in ipairs(skeletonConnections) do
		local partA = model:FindFirstChild(connection[1])
		local partB = model:FindFirstChild(connection[2])

		if partA and partB then
			createSkeletonPart(folder, partA, partB)
		end
	end
end

--==================================================
-- AIM TARGET
--==================================================

local function getClosestTarget()
	local closest = nil
	local closestScreenDistance = math.huge

	local viewportCenter =
		Vector2.new(
			Camera.ViewportSize.X / 2,
			Camera.ViewportSize.Y / 2
		)

	for _, npc in ipairs(getNPCs()) do
		local head = getHead(npc)

		if head then
			local worldDistance = getDistance(npc)

			if worldDistance <= CONFIG.AimDistance then

				local screenPosition, visible =
					Camera:WorldToViewportPoint(head.Position)

				if visible and screenPosition.Z > 0 then

					local screenPoint =
						Vector2.new(
							screenPosition.X,
							screenPosition.Y
						)

					local screenDistance =
						(screenPoint - viewportCenter).Magnitude

					if screenDistance <= CONFIG.AimFOV then

						if screenDistance < closestScreenDistance then
							closestScreenDistance = screenDistance
							closest = npc
						end
					end
				end
			end
		end
	end

	return closest
end

--==================================================
-- AIM LOOP
--==================================================

RunService.RenderStepped:Connect(function()
	if not CONFIG.AimEnabled then
		return
	end

	local character = LocalPlayer.Character

	if not character then
		return
	end

	local humanoid = getHumanoid(character)

	if not humanoid or humanoid.Health <= 0 then
		return
	end

	local target = getClosestTarget()

	if target then
		local head = getHead(target)

		if head then
			local cameraPosition = Camera.CFrame.Position

			local desired =
				CFrame.lookAt(
					cameraPosition,
					head.Position
				)

			Camera.CFrame =
				Camera.CFrame:Lerp(
					desired,
					CONFIG.AimSmoothness
				)
		end
	end
end)

--==================================================
-- ESP LOOP
--==================================================

RunService.RenderStepped:Connect(function()
	local currentNPCs = {}

	for _, npc in ipairs(getNPCs()) do
		currentNPCs[npc] = true

		local data = createESP(npc)

		if data then
			local distance = getDistance(npc)
			local humanoid = getHumanoid(npc)

			local visible =
				CONFIG.ESPEnabled
				and distance <= CONFIG.ESPDistance

			data.billboard.Enabled = visible
			data.highlight.Enabled = visible

			if visible then
				data.nameLabel.Text = npc.Name

				data.distanceLabel.Text =
					math.floor(distance) .. " studs"

				if humanoid then
					local health =
						math.clamp(
							humanoid.Health /
							math.max(humanoid.MaxHealth, 1),
							0,
							1
						)

					data.healthBar.Size =
						UDim2.new(
							health,
							0,
							1,
							0
						)

					if health <= 0.25 then
						data.healthBar.BackgroundColor3 = RED
					elseif health <= 0.5 then
						data.healthBar.BackgroundColor3 =
							Color3.fromRGB(255, 190, 60)
					else
						data.healthBar.BackgroundColor3 = GREEN
					end
				end
			end

			updateSkeleton(npc, data)
		end
	end

	for npc in pairs(ESPObjects) do
		if not currentNPCs[npc] or not npc.Parent then
			destroyESP(npc)
		end
	end
end)

--==================================================
-- NOCLIP
--==================================================

RunService.Stepped:Connect(function()
	if not CONFIG.NoclipEnabled then
		return
	end

	local character = LocalPlayer.Character

	if not character then
		return
	end

	for _, object in ipairs(character:GetDescendants()) do
		if object:IsA("BasePart") then
			object.CanCollide = false
		end
	end
end)

--==================================================
-- RIGHT SHIFT
--==================================================

UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then
		return
	end

	if input.KeyCode == Enum.KeyCode.RightShift then

		CONFIG.PanelOpen = not CONFIG.PanelOpen

		Main.Visible = CONFIG.PanelOpen
	end
end)

--==================================================
-- PAINEL ARRASTÁVEL
--==================================================

local dragging = false
local dragStart
local startPosition

Header.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then

		dragging = true
		dragStart = input.Position
		startPosition = Main.Position

		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if not dragging then
		return
	end

	if input.UserInputType ~= Enum.UserInputType.MouseMovement then
		return
	end

	local delta =
		input.Position - dragStart

	Main.Position =
		UDim2.new(
			startPosition.X.Scale,
			startPosition.X.Offset + delta.X,
			startPosition.Y.Scale,
			startPosition.Y.Offset + delta.Y
		)
end)

--==================================================
-- ATUALIZAÇÃO DO FOV
--==================================================

RunService.RenderStepped:Connect(function()
	FOVCircle.Position =
		UDim2.fromOffset(
			Camera.ViewportSize.X / 2,
			Camera.ViewportSize.Y / 2
		)
end)

--==================================================
-- LIMPEZA AO REMOVER NPC
--==================================================

Workspace.ChildRemoved:Connect(function(child)
	if child.Name == CONFIG.NPCFolderName then

		for npc in pairs(ESPObjects) do
			destroyESP(npc)
		end
	end
end)

--==================================================
-- CRIA PASTA AUTOMATICAMENTE
--==================================================

if not Workspace:FindFirstChild(CONFIG.NPCFolderName) then
	local folder = Instance.new("Folder")
	folder.Name = CONFIG.NPCFolderName
	folder.Parent = Workspace
end

print("==========================================")
print(" NEON AIM SIMULATOR")
print(" NPC TRAINING MODE")
print(" RightShift = Abrir/Fechar")
print(" Pasta: workspace.TrainingNPCs")
print("==========================================")
