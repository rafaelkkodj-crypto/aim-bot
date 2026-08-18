--========================================================
-- NEON PLAYER TRAINING SYSTEM
-- Roblox Studio - versão Player R6/R15
-- UM ÚNICO LOCALSCRIPT
--
-- Coloque em:
-- StarterPlayer > StarterPlayerScripts
--========================================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

--========================================================
-- CONFIG
--========================================================

local Config = {
	Aim = false,
	AimDistance = 200,
	AimFOV = 120,
	AimSmooth = 18,

	AllCheck = false,
	AllCheckDistance = 3000,

	ESP = false,
	ESPDistance = 3000,

	Skeleton = false,
	SkeletonDistance = 3000,

	Noclip = false,
}

--========================================================
-- CORES
--========================================================

local PINK = Color3.fromRGB(255, 20, 180)
local PURPLE = Color3.fromRGB(145, 20, 220)

local DARK = Color3.fromRGB(12, 7, 18)
local DARK2 = Color3.fromRGB(27, 12, 35)

local WHITE = Color3.fromRGB(255, 255, 255)
local GREEN = Color3.fromRGB(70, 255, 130)
local RED = Color3.fromRGB(255, 60, 80)
local YELLOW = Color3.fromRGB(255, 210, 60)

--========================================================
-- GUI
--========================================================

local Gui = Instance.new("ScreenGui")
Gui.Name = "NeonPlayerTraining"
Gui.ResetOnSpawn = false
Gui.IgnoreGuiInset = true
Gui.Parent = PlayerGui

--========================================================
-- FOV
--========================================================

local FOV = Instance.new("Frame")
FOV.Name = "FOV"
FOV.AnchorPoint = Vector2.new(0.5, 0.5)
FOV.Position = UDim2.fromScale(0.5, 0.5)
FOV.Size = UDim2.fromOffset(Config.AimFOV * 2, Config.AimFOV * 2)
FOV.BackgroundTransparency = 1
FOV.BorderSizePixel = 0
FOV.ZIndex = 2
FOV.Parent = Gui

local FOVCorner = Instance.new("UICorner")
FOVCorner.CornerRadius = UDim.new(1, 0)
FOVCorner.Parent = FOV

local FOVStroke = Instance.new("UIStroke")
FOVStroke.Color = PINK
FOVStroke.Thickness = 2
FOVStroke.Transparency = 0.1
FOVStroke.Parent = FOV

--========================================================
-- PAINEL
--========================================================

local Main = Instance.new("Frame")
Main.Name = "Main"
Main.Size = UDim2.fromOffset(430, 600)
Main.Position = UDim2.new(0.5, -215, 0.5, -300)
Main.BackgroundColor3 = DARK
Main.BorderSizePixel = 0
Main.ZIndex = 10
Main.Parent = Gui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 15)
MainCorner.Parent = Main

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = PINK
MainStroke.Thickness = 2
MainStroke.Parent = Main

--========================================================
-- HEADER
--========================================================

local Header = Instance.new("Frame")
Header.Size = UDim2.new(1, 0, 0, 70)
Header.BackgroundColor3 = DARK2
Header.BorderSizePixel = 0
Header.ZIndex = 11
Header.Parent = Main

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 15)
HeaderCorner.Parent = Header

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -20, 0, 30)
Title.Position = UDim2.fromOffset(15, 7)
Title.BackgroundTransparency = 1
Title.Text = "NEON PLAYER SYSTEM"
Title.TextColor3 = PINK
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.ZIndex = 12
Title.Parent = Header

local Subtitle = Instance.new("TextLabel")
Subtitle.Size = UDim2.new(1, -20, 0, 20)
Subtitle.Position = UDim2.fromOffset(15, 38)
Subtitle.BackgroundTransparency = 1
Subtitle.Text = "R6 / R15  •  PLAYER TRAINING"
Subtitle.TextColor3 = Color3.fromRGB(190, 150, 200)
Subtitle.Font = Enum.Font.Gotham
Subtitle.TextSize = 11
Subtitle.TextXAlignment = Enum.TextXAlignment.Left
Subtitle.ZIndex = 12
Subtitle.Parent = Header

--========================================================
-- SCROLL
--========================================================

local Scroll = Instance.new("ScrollingFrame")
Scroll.Size = UDim2.new(1, -20, 1, -82)
Scroll.Position = UDim2.fromOffset(10, 77)
Scroll.BackgroundTransparency = 1
Scroll.BorderSizePixel = 0
Scroll.ScrollBarThickness = 4
Scroll.ScrollBarImageColor3 = PINK
Scroll.CanvasSize = UDim2.new(0, 0, 0, 850)
Scroll.ZIndex = 11
Scroll.Parent = Main

local Layout = Instance.new("UIListLayout")
Layout.Padding = UDim.new(0, 8)
Layout.Parent = Scroll

--========================================================
-- GUI HELPERS
--========================================================

local function Section(text)

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, -10, 0, 28)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = PINK
	label.Font = Enum.Font.GothamBold
	label.TextSize = 15
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.ZIndex = 12
	label.Parent = Scroll

	return label
end

local function Toggle(text, default, callback)

	local button = Instance.new("TextButton")
	button.Size = UDim2.new(1, -10, 0, 42)
	button.BackgroundColor3 = DARK2
	button.BorderSizePixel = 0
	button.Text = ""
	button.AutoButtonColor = false
	button.ZIndex = 12
	button.Parent = Scroll

	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, 8)
	corner.Parent = button

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, -85, 1, 0)
	label.Position = UDim2.fromOffset(15, 0)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = WHITE
	label.Font = Enum.Font.GothamMedium
	label.TextSize = 13
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.ZIndex = 13
	label.Parent = button

	local status = Instance.new("TextLabel")
	status.Size = UDim2.fromOffset(55, 25)
	status.Position = UDim2.new(1, -67, 0.5, -12)
	status.BackgroundColor3 = Color3.fromRGB(55, 25, 60)
	status.Text = "OFF"
	status.TextColor3 = Color3.fromRGB(180, 150, 180)
	status.Font = Enum.Font.GothamBold
	status.TextSize = 11
	status.ZIndex = 13
	status.Parent = button

	local statusCorner = Instance.new("UICorner")
	statusCorner.CornerRadius = UDim.new(0, 6)
	statusCorner.Parent = status

	local value = default

	local function update()

		if value then
			status.Text = "ON"
			status.BackgroundColor3 = PINK
			status.TextColor3 = WHITE
		else
			status.Text = "OFF"
			status.BackgroundColor3 = Color3.fromRGB(55, 25, 60)
			status.TextColor3 = Color3.fromRGB(180, 150, 180)
		end

		callback(value)
	end

	button.MouseButton1Click:Connect(function()
		value = not value
		update()
	end)

	update()

	return button
end

local function Slider(text, min, max, default, callback)

	local holder = Instance.new("Frame")
	holder.Size = UDim2.new(1, -10, 0, 68)
	holder.BackgroundColor3 = DARK2
	holder.BorderSizePixel = 0
	holder.ZIndex = 12
	holder.Parent = Scroll

	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, 8)
	corner.Parent = holder

	local title = Instance.new("TextLabel")
	title.Size = UDim2.new(1, -100, 0, 24)
	title.Position = UDim2.fromOffset(15, 5)
	title.BackgroundTransparency = 1
	title.Text = text
	title.TextColor3 = WHITE
	title.Font = Enum.Font.GothamMedium
	title.TextSize = 13
	title.TextXAlignment = Enum.TextXAlignment.Left
	title.ZIndex = 13
	title.Parent = holder

	local valueText = Instance.new("TextLabel")
	valueText.Size = UDim2.fromOffset(80, 24)
	valueText.Position = UDim2.new(1, -90, 0, 5)
	valueText.BackgroundTransparency = 1
	valueText.TextColor3 = PINK
	valueText.Font = Enum.Font.GothamBold
	valueText.TextSize = 13
	valueText.TextXAlignment = Enum.TextXAlignment.Right
	valueText.ZIndex = 13
	valueText.Parent = holder

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
	fill.BackgroundColor3 = PINK
	fill.BorderSizePixel = 0
	fill.ZIndex = 14
	fill.Parent = bar

	local fillCorner = Instance.new("UICorner")
	fillCorner.CornerRadius = UDim.new(1, 0)
	fillCorner.Parent = fill

	local dragging = false

	local function setValue(value)

		value = math.clamp(math.floor(value + 0.5), min, max)

		local percent = (value - min) / (max - min)

		fill.Size = UDim2.new(percent, 0, 1, 0)
		valueText.Text = tostring(value)

		callback(value)
	end

	local function fromMouse(x)

		local startX = bar.AbsolutePosition.X
		local width = bar.AbsoluteSize.X

		local percent = math.clamp(
			(x - startX) / width,
			0,
			1
		)

		setValue(min + (max - min) * percent)
	end

	bar.InputBegan:Connect(function(input)

		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			fromMouse(input.Position.X)
		end
	end)

	UserInputService.InputChanged:Connect(function(input)

		if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
			fromMouse(input.Position.X)
		end
	end)

	UserInputService.InputEnded:Connect(function(input)

		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = false
		end
	end)

	setValue(default)

	return holder
end

--========================================================
-- AIM
--========================================================

Section("🎯 AIM ASSIST")

Toggle(
	"Aim Assist • Player Head",
	false,
	function(value)
		Config.Aim = value
	end
)

Slider(
	"Aim Distance",
	1,
	200,
	200,
	function(value)
		Config.AimDistance = value
	end
)

Slider(
	"Aim FOV",
	1,
	500,
	120,
	function(value)

		Config.AimFOV = value

		FOV.Size = UDim2.fromOffset(
			value * 2,
			value * 2
		)
	end
)

Slider(
	"Aim Smoothness",
	1,
	100,
	18,
	function(value)
		Config.AimSmooth = value / 100
	end
)

--========================================================
-- ALL CHECK
--========================================================

Section("🔎 ALL CHECK")

Toggle(
	"All Check • Visibility",
	false,
	function(value)
		Config.AllCheck = value
	end
)

Slider(
	"All Check Distance",
	1,
	3000,
	3000,
	function(value)
		Config.AllCheckDistance = value
	end
)

--========================================================
-- ESP
--========================================================

Section("👁 ESP")

Toggle(
	"ESP • Name / Health / Distance",
	false,
	function(value)
		Config.ESP = value
	end
)

Slider(
	"ESP Distance",
	1,
	3000,
	3000,
	function(value)
		Config.ESPDistance = value
	end
)

--========================================================
-- SKELETON
--========================================================

Section("🦴 SKELETON")

Toggle(
	"Skeleton • R6 / R15",
	false,
	function(value)
		Config.Skeleton = value
	end
)

Slider(
	"Skeleton Distance",
	1,
	3000,
	3000,
	function(value)
		Config.SkeletonDistance = value
	end
)

--========================================================
-- NOCLIP
--========================================================

Section("🌀 MOVEMENT")

Toggle(
	"Noclip",
	false,
	function(value)
		Config.Noclip = value
	end
)

--========================================================
-- PLAYER UTILITIES
--========================================================

local function GetCharacter(player)
	return player and player.Character
end

local function GetHumanoid(player)

	local character = GetCharacter(player)

	return character
		and character:FindFirstChildOfClass("Humanoid")
end

local function GetRoot(player)

	local character = GetCharacter(player)

	if not character then
		return nil
	end

	return character:FindFirstChild("HumanoidRootPart")
		or character:FindFirstChild("Torso")
end

local function GetHead(player)

	local character = GetCharacter(player)

	return character
		and character:FindFirstChild("Head")
end

local function IsAlive(player)

	local humanoid = GetHumanoid(player)

	return humanoid
		and humanoid.Health > 0
end

local function DistanceTo(player)

	local myRoot = GetRoot(LocalPlayer)
	local targetRoot = GetRoot(player)

	if not myRoot or not targetRoot then
		return math.huge
	end

	return (myRoot.Position - targetRoot.Position).Magnitude
end

--========================================================
-- ALL CHECK
--========================================================

local CheckObjects = {}

local function RemoveCheck(player)

	local object = CheckObjects[player]

	if object then
		object:Destroy()
		CheckObjects[player] = nil
	end
end

local function CanSee(player)

	local character = GetCharacter(player)
	local head = GetHead(player)

	if not character or not head then
		return false
	end

	local myCharacter = GetCharacter(LocalPlayer)

	local origin = Camera.CFrame.Position
	local direction = head.Position - origin

	local params = RaycastParams.new()

	params.FilterType = Enum.RaycastFilterType.Exclude

	params.FilterDescendantsInstances = {
		myCharacter
	}

	local result = workspace:Raycast(
		origin,
		direction,
		params
	)

	if not result then
		return true
	end

	return result.Instance:IsDescendantOf(character)
end

local function UpdateAllCheck(player)

	if player == LocalPlayer then
		return
	end

	if not Config.AllCheck then
		RemoveCheck(player)
		return
	end

	if not IsAlive(player) then
		RemoveCheck(player)
		return
	end

	local distance = DistanceTo(player)

	if distance > Config.AllCheckDistance then
		RemoveCheck(player)
		return
	end

	local character = GetCharacter(player)

	if not character then
		return
	end

	local highlight = CheckObjects[player]

	if not highlight then

		highlight = Instance.new("Highlight")
		highlight.Name = "AllCheck"
		highlight.Adornee = character
		highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
		highlight.FillTransparency = 0.82
		highlight.OutlineTransparency = 0.05
		highlight.Parent = character

		CheckObjects[player] = highlight
	end

	if CanSee(player) then
		highlight.FillColor = GREEN
		highlight.OutlineColor = GREEN
	else
		highlight.FillColor = RED
		highlight.OutlineColor = RED
	end
end

--========================================================
-- ESP
--========================================================

local ESPObjects = {}

local function RemoveESP(player)

	local data = ESPObjects[player]

	if data then

		if data.Gui then
			data.Gui:Destroy()
		end

		ESPObjects[player] = nil
	end
end

local function CreateESP(player)

	local character = GetCharacter(player)
	local head = GetHead(player)

	if not character or not head then
		return nil
	end

	local old = ESPObjects[player]

	if old
		and old.Character == character
		and old.Gui
		and old.Gui.Parent then

		old.Gui.Adornee = head

		return old
	end

	RemoveESP(player)

	local billboard = Instance.new("BillboardGui")

	billboard.Name = "PlayerESP"
	billboard.Adornee = head
	billboard.Size = UDim2.fromOffset(220, 90)
	billboard.StudsOffset = Vector3.new(0, 3, 0)
	billboard.AlwaysOnTop = true
	billboard.MaxDistance = Config.ESPDistance + 100
	billboard.Enabled = false
	billboard.Parent = Gui

	local name = Instance.new("TextLabel")

	name.Size = UDim2.new(1, 0, 0, 23)
	name.BackgroundTransparency = 1
	name.TextColor3 = PINK
	name.TextStrokeColor3 = Color3.new(0, 0, 0)
	name.TextStrokeTransparency = 0
	name.Font = Enum.Font.GothamBold
	name.TextSize = 14
	name.Parent = billboard

	local distance = Instance.new("TextLabel")

	distance.Size = UDim2.new(1, 0, 0, 18)
	distance.Position = UDim2.fromOffset(0, 23)
	distance.BackgroundTransparency = 1
	distance.TextColor3 = WHITE
	distance.TextStrokeColor3 = Color3.new(0, 0, 0)
	distance.TextStrokeTransparency = 0
	distance.Font = Enum.Font.Gotham
	distance.TextSize = 11
	distance.Parent = billboard

	local healthBack = Instance.new("Frame")

	healthBack.Size = UDim2.new(0.72, 0, 0, 7)
	healthBack.Position = UDim2.new(0.14, 0, 0, 46)
	healthBack.BackgroundColor3 = Color3.fromRGB(45, 20, 50)
	healthBack.BorderSizePixel = 0
	healthBack.Parent = billboard

	local healthBackCorner = Instance.new("UICorner")
	healthBackCorner.CornerRadius = UDim.new(1, 0)
	healthBackCorner.Parent = healthBack

	local health = Instance.new("Frame")

	health.Size = UDim2.new(1, 0, 1, 0)
	health.BackgroundColor3 = GREEN
	health.BorderSizePixel = 0
	health.Parent = healthBack

	local healthCorner = Instance.new("UICorner")
	healthCorner.CornerRadius = UDim.new(1, 0)
	healthCorner.Parent = health

	local healthText = Instance.new("TextLabel")

	healthText.Size = UDim2.new(1, 0, 0, 18)
	healthText.Position = UDim2.fromOffset(0, 55)
	healthText.BackgroundTransparency = 1
	healthText.TextColor3 = WHITE
	healthText.TextStrokeColor3 = Color3.new(0, 0, 0)
	healthText.TextStrokeTransparency = 0
	healthText.Font = Enum.Font.Gotham
	healthText.TextSize = 10
	healthText.Parent = billboard

	local data = {
		Gui = billboard,
		Character = character,
		Head = head,
		Name = name,
		Distance = distance,
		Health = health,
		HealthText = healthText,
	}

	ESPObjects[player] = data

	return data
end

local function UpdateESP(player)

	if player == LocalPlayer then
		return
	end

	if not Config.ESP then

		if ESPObjects[player] then
			ESPObjects[player].Gui.Enabled = false
		end

		return
	end

	if not IsAlive(player) then
		RemoveESP(player)
		return
	end

	local distance = DistanceTo(player)

	if distance > Config.ESPDistance then

		if ESPObjects[player] then
			ESPObjects[player].Gui.Enabled = false
		end

		return
	end

	local data = CreateESP(player)

	if not data then
		return
	end

	local character = GetCharacter(player)
	local head = GetHead(player)
	local humanoid = GetHumanoid(player)

	if not character or not head or not humanoid then
		return
	end

	data.Gui.Adornee = head
	data.Gui.MaxDistance = Config.ESPDistance + 100
	data.Gui.Enabled = true

	data.Name.Text =
		player.DisplayName ..
		"  @" ..
		player.Name

	data.Distance.Text =
		math.floor(distance) ..
		" studs"

	local maxHealth = math.max(humanoid.MaxHealth, 1)

	local healthPercent =
		math.clamp(
			humanoid.Health / maxHealth,
			0,
			1
		)

	data.Health.Size =
		UDim2.new(
			healthPercent,
			0,
			1,
			0
		)

	data.HealthText.Text =
		math.floor(humanoid.Health) ..
		" / " ..
		math.floor(humanoid.MaxHealth)

	if healthPercent <= 0.25 then
		data.Health.BackgroundColor3 = RED
	elseif healthPercent <= 0.5 then
		data.Health.BackgroundColor3 = YELLOW
	else
		data.Health.BackgroundColor3 = GREEN
	end
end

--========================================================
-- SKELETON
--========================================================

local SkeletonObjects = {}

local R6Bones = {
	{"Head", "Torso"},
	{"Torso", "Left Arm"},
	{"Torso", "Right Arm"},
	{"Torso", "Left Leg"},
	{"Torso", "Right Leg"},
}

local R15Bones = {
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

local function RemoveSkeleton(player)

	local folder = SkeletonObjects[player]

	if folder then
		folder:Destroy()
		SkeletonObjects[player] = nil
	end
end

local function CreateBone(folder, partA, partB)

	if not partA or not partB then
		return
	end

	local distance =
		(partA.Position - partB.Position).Magnitude

	if distance <= 0 then
		return
	end

	local midpoint =
		(partA.Position + partB.Position) / 2

	local bone = Instance.new("Part")

	bone.Name = "SkeletonBone"
	bone.Anchored = true
	bone.CanCollide = false
	bone.CanTouch = false
	bone.CanQuery = false
	bone.CastShadow = false
	bone.Material = Enum.Material.Neon
	bone.Color = PINK
	bone.Size = Vector3.new(
		0.06,
		0.06,
		distance
	)

	bone.CFrame =
		CFrame.lookAt(
			midpoint,
			partB.Position
		)

	bone.Parent = folder
end

local function UpdateSkeleton(player)

	if player == LocalPlayer then
		return
	end

	if not Config.Skeleton then
		RemoveSkeleton(player)
		return
	end

	if not IsAlive(player) then
		RemoveSkeleton(player)
		return
	end

	local distance = DistanceTo(player)

	if distance > Config.SkeletonDistance then
		RemoveSkeleton(player)
		return
	end

	local character = GetCharacter(player)
	local humanoid = GetHumanoid(player)

	if not character or not humanoid then
		return
	end

	-- Recria apenas se necessário.
	local oldFolder = SkeletonObjects[player]

	if oldFolder then
		for _, bone in ipairs(oldFolder:GetChildren()) do
			bone:Destroy()
		end
	else
		oldFolder = Instance.new("Folder")
		oldFolder.Name = "PlayerSkeleton"
		oldFolder.Parent = workspace

		SkeletonObjects[player] = oldFolder
	end

	local bones

	if humanoid.RigType == Enum.HumanoidRigType.R15 then
		bones = R15Bones
	else
		bones = R6Bones
	end

	for _, pair in ipairs(bones) do

		local a = character:FindFirstChild(pair[1])
		local b = character:FindFirstChild(pair[2])

		CreateBone(
			oldFolder,
			a,
			b
		)
	end
end

--========================================================
-- AIM
--========================================================

local function GetAimTarget()

	local closestPlayer = nil
	local closestFOV = math.huge

	local viewport = Camera.ViewportSize

	local center = Vector2.new(
		viewport.X / 2,
		viewport.Y / 2
	)

	for _, player in ipairs(Players:GetPlayers()) do

		if player ~= LocalPlayer
			and IsAlive(player) then

			local distance = DistanceTo(player)

			if distance <= Config.AimDistance then

				local head = GetHead(player)

				if head then

					local screenPosition, visible =
						Camera:WorldToViewportPoint(
							head.Position
						)

					if visible
						and screenPosition.Z > 0 then

						local point =
							Vector2.new(
								screenPosition.X,
								screenPosition.Y
							)

						local fovDistance =
							(point - center).Magnitude

						if fovDistance <= Config.AimFOV then

							local allowed = true

							if Config.AllCheck then
								allowed = CanSee(player)
							end

							if allowed
								and fovDistance < closestFOV then

								closestFOV = fovDistance
								closestPlayer = player
							end
						end
					end
				end
			end
		end
	end

	return closestPlayer
end

RunService:BindToRenderStep(
	"NeonAim",
	Enum.RenderPriority.Camera.Value + 1,
	function()

		if not Config.Aim then
			return
		end

		local humanoid = GetHumanoid(LocalPlayer)

		if not humanoid or humanoid.Health <= 0 then
			return
		end

		local target = GetAimTarget()

		if not target then
			return
		end

		local head = GetHead(target)

		if not head then
			return
		end

		local cameraPosition = Camera.CFrame.Position

		local desired =
			CFrame.lookAt(
				cameraPosition,
				head.Position
			)

		Camera.CFrame =
			Camera.CFrame:Lerp(
				desired,
				Config.AimSmooth
			)
	end
)

--========================================================
-- UPDATE LOOP
--========================================================

local skeletonClock = 0

RunService.RenderStepped:Connect(function(delta)

	for _, player in ipairs(Players:GetPlayers()) do

		if player ~= LocalPlayer then

			UpdateESP(player)
			UpdateAllCheck(player)

		end
	end

	skeletonClock += delta

	if skeletonClock >= 0.08 then

		skeletonClock = 0

		for _, player in ipairs(Players:GetPlayers()) do

			if player ~= LocalPlayer then
				UpdateSkeleton(player)
			end
		end
	end
end)

--========================================================
-- NOCLIP
--========================================================

RunService.Stepped:Connect(function()

	if not Config.Noclip then
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

--========================================================
-- RESPAWN
--========================================================

local function SetupPlayer(player)

	if player == LocalPlayer then
		return
	end

	player.CharacterRemoving:Connect(function()

		RemoveESP(player)
		RemoveCheck(player)
		RemoveSkeleton(player)

	end)

	player.CharacterAdded:Connect(function()

		RemoveESP(player)
		RemoveCheck(player)
		RemoveSkeleton(player)

		task.wait(0.5)

	end)
end

for _, player in ipairs(Players:GetPlayers()) do
	SetupPlayer(player)
end

Players.PlayerAdded:Connect(SetupPlayer)

Players.PlayerRemoving:Connect(function(player)

	RemoveESP(player)
	RemoveCheck(player)
	RemoveSkeleton(player)

end)

--========================================================
-- ARRASTAR PAINEL
--========================================================

local dragging = false
local dragStart
local startPosition

Header.InputBegan:Connect(function(input)

	if input.UserInputType ==
		Enum.UserInputType.MouseButton1 then

		dragging = true
		dragStart = input.Position
		startPosition = Main.Position

	end
end)

Header.InputEnded:Connect(function(input)

	if input.UserInputType ==
		Enum.UserInputType.MouseButton1 then

		dragging = false

	end
end)

UserInputService.InputChanged:Connect(function(input)

	if not dragging then
		return
	end

	if input.UserInputType ~=
		Enum.UserInputType.MouseMovement then
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

--========================================================
-- RIGHT SHIFT
--========================================================

UserInputService.InputBegan:Connect(function(input, processed)

	if processed then
		return
	end

	if input.KeyCode == Enum.KeyCode.RightShift then

		Main.Visible = not Main.Visible

	end
end)

--========================================================
-- FOV POSITION
--========================================================

RunService.RenderStepped:Connect(function()

	local viewport = Camera.ViewportSize

	FOV.Position =
		UDim2.fromOffset(
			viewport.X / 2,
			viewport.Y / 2
		)

end)

--========================================================
-- LIMPEZA
--========================================================

LocalPlayer.CharacterRemoving:Connect(function()

	for player in pairs(ESPObjects) do
		RemoveESP(player)
	end

	for player in pairs(CheckObjects) do
		RemoveCheck(player)
	end

	for player in pairs(SkeletonObjects) do
		RemoveSkeleton(player)
	end
end)

print("======================================")
print(" NEON PLAYER TRAINING SYSTEM")
print(" ESP: READY")
print(" SKELETON R6/R15: READY")
print(" AIM ASSIST: READY")
print(" ALL CHECK: READY")
print("======================================")
