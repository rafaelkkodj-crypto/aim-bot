--========================================================
-- NEON PLAYER TRAINING SYSTEM
-- UM ÚNICO LOCALSCRIPT
-- Coloque em:
-- StarterPlayer > StarterPlayerScripts
--
-- Compatível com Players R6/R15
-- RightShift = abrir/fechar painel
--========================================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

--========================================================
-- CONFIGURAÇÕES
--========================================================

local Settings = {
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

	Panel = true,
}

--========================================================
-- CORES
--========================================================

local Pink = Color3.fromRGB(255, 20, 180)
local Purple = Color3.fromRGB(150, 20, 220)
local Dark = Color3.fromRGB(14, 8, 20)
local Dark2 = Color3.fromRGB(27, 12, 34)
local White = Color3.fromRGB(255, 255, 255)
local Green = Color3.fromRGB(70, 255, 130)
local Red = Color3.fromRGB(255, 60, 80)
local Yellow = Color3.fromRGB(255, 210, 60)

--========================================================
-- GUI
--========================================================

local Gui = Instance.new("ScreenGui")
Gui.Name = "NeonPlayerSystem"
Gui.ResetOnSpawn = false
Gui.IgnoreGuiInset = true
Gui.Parent = LocalPlayer:WaitForChild("PlayerGui")

--========================================================
-- FOV
--========================================================

local FOV = Instance.new("Frame")
FOV.Name = "FOV"
FOV.AnchorPoint = Vector2.new(0.5, 0.5)
FOV.Position = UDim2.fromScale(0.5, 0.5)
FOV.Size = UDim2.fromOffset(Settings.AimFOV * 2, Settings.AimFOV * 2)
FOV.BackgroundTransparency = 1
FOV.BorderSizePixel = 0
FOV.Visible = true
FOV.ZIndex = 1
FOV.Parent = Gui

local FOVCorner = Instance.new("UICorner")
FOVCorner.CornerRadius = UDim.new(1, 0)
FOVCorner.Parent = FOV

local FOVStroke = Instance.new("UIStroke")
FOVStroke.Color = Pink
FOVStroke.Thickness = 2
FOVStroke.Transparency = 0.15
FOVStroke.Parent = FOV

--========================================================
-- PAINEL
--========================================================

local Main = Instance.new("Frame")
Main.Name = "Main"
Main.Size = UDim2.fromOffset(430, 590)
Main.Position = UDim2.new(0.5, -215, 0.5, -295)
Main.BackgroundColor3 = Dark
Main.BorderSizePixel = 0
Main.ZIndex = 10
Main.Parent = Gui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 15)
MainCorner.Parent = Main

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Pink
MainStroke.Thickness = 2
MainStroke.Parent = Main

--========================================================
-- HEADER
--========================================================

local Header = Instance.new("Frame")
Header.Size = UDim2.new(1, 0, 0, 72)
Header.BackgroundColor3 = Dark2
Header.BorderSizePixel = 0
Header.ZIndex = 11
Header.Parent = Main

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 15)
HeaderCorner.Parent = Header

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -25, 0, 30)
Title.Position = UDim2.fromOffset(15, 8)
Title.BackgroundTransparency = 1
Title.Text = "NEON PLAYER SYSTEM"
Title.TextColor3 = Pink
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.ZIndex = 12
Title.Parent = Header

local Subtitle = Instance.new("TextLabel")
Subtitle.Size = UDim2.new(1, -25, 0, 20)
Subtitle.Position = UDim2.fromOffset(15, 40)
Subtitle.BackgroundTransparency = 1
Subtitle.Text = "PLAYER • R6 / R15 • RIGHTSHIFT"
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
Scroll.Size = UDim2.new(1, -20, 1, -85)
Scroll.Position = UDim2.fromOffset(10, 80)
Scroll.BackgroundTransparency = 1
Scroll.BorderSizePixel = 0
Scroll.ScrollBarThickness = 4
Scroll.ScrollBarImageColor3 = Pink
Scroll.CanvasSize = UDim2.new(0, 0, 0, 1000)
Scroll.ZIndex = 11
Scroll.Parent = Main

local Layout = Instance.new("UIListLayout")
Layout.Padding = UDim.new(0, 8)
Layout.SortOrder = Enum.SortOrder.LayoutOrder
Layout.Parent = Scroll

--========================================================
-- FUNÇÕES GUI
--========================================================

local function Section(text)

	local Label = Instance.new("TextLabel")
	Label.Size = UDim2.new(1, -10, 0, 30)
	Label.BackgroundTransparency = 1
	Label.Text = text
	Label.TextColor3 = Pink
	Label.Font = Enum.Font.GothamBold
	Label.TextSize = 15
	Label.TextXAlignment = Enum.TextXAlignment.Left
	Label.ZIndex = 12
	Label.Parent = Scroll

	return Label
end

local function Toggle(text, initial, callback)

	local Button = Instance.new("TextButton")
	Button.Size = UDim2.new(1, -10, 0, 43)
	Button.BackgroundColor3 = Dark2
	Button.BorderSizePixel = 0
	Button.Text = ""
	Button.AutoButtonColor = false
	Button.ZIndex = 12
	Button.Parent = Scroll

	local Corner = Instance.new("UICorner")
	Corner.CornerRadius = UDim.new(0, 8)
	Corner.Parent = Button

	local Label = Instance.new("TextLabel")
	Label.Size = UDim2.new(1, -85, 1, 0)
	Label.Position = UDim2.fromOffset(15, 0)
	Label.BackgroundTransparency = 1
	Label.Text = text
	Label.TextColor3 = White
	Label.Font = Enum.Font.GothamMedium
	Label.TextSize = 13
	Label.TextXAlignment = Enum.TextXAlignment.Left
	Label.ZIndex = 13
	Label.Parent = Button

	local Status = Instance.new("TextLabel")
	Status.Size = UDim2.fromOffset(55, 25)
	Status.Position = UDim2.new(1, -67, 0.5, -12)
	Status.BackgroundColor3 = Color3.fromRGB(55, 25, 60)
	Status.TextColor3 = Color3.fromRGB(180, 150, 180)
	Status.Font = Enum.Font.GothamBold
	Status.TextSize = 11
	Status.ZIndex = 13
	Status.Parent = Button

	local StatusCorner = Instance.new("UICorner")
	StatusCorner.CornerRadius = UDim.new(0, 6)
	StatusCorner.Parent = Status

	local Value = initial

	local function update()
		Status.Text = Value and "ON" or "OFF"

		if Value then
			Status.BackgroundColor3 = Pink
			Status.TextColor3 = White
		else
			Status.BackgroundColor3 = Color3.fromRGB(55, 25, 60)
			Status.TextColor3 = Color3.fromRGB(180, 150, 180)
		end

		callback(Value)
	end

	Button.MouseButton1Click:Connect(function()
		Value = not Value
		update()
	end)

	update()

	return Button
end

local function Slider(text, minimum, maximum, default, callback)

	local Holder = Instance.new("Frame")
	Holder.Size = UDim2.new(1, -10, 0, 68)
	Holder.BackgroundColor3 = Dark2
	Holder.BorderSizePixel = 0
	Holder.ZIndex = 12
	Holder.Parent = Scroll

	local Corner = Instance.new("UICorner")
	Corner.CornerRadius = UDim.new(0, 8)
	Corner.Parent = Holder

	local Label = Instance.new("TextLabel")
	Label.Size = UDim2.new(1, -100, 0, 25)
	Label.Position = UDim2.fromOffset(15, 5)
	Label.BackgroundTransparency = 1
	Label.Text = text
	Label.TextColor3 = White
	Label.Font = Enum.Font.GothamMedium
	Label.TextSize = 13
	Label.TextXAlignment = Enum.TextXAlignment.Left
	Label.ZIndex = 13
	Label.Parent = Holder

	local ValueLabel = Instance.new("TextLabel")
	ValueLabel.Size = UDim2.fromOffset(80, 25)
	ValueLabel.Position = UDim2.new(1, -90, 0, 5)
	ValueLabel.BackgroundTransparency = 1
	ValueLabel.TextColor3 = Pink
	ValueLabel.Font = Enum.Font.GothamBold
	ValueLabel.TextSize = 13
	ValueLabel.TextXAlignment = Enum.TextXAlignment.Right
	ValueLabel.ZIndex = 13
	ValueLabel.Parent = Holder

	local Bar = Instance.new("Frame")
	Bar.Size = UDim2.new(1, -30, 0, 7)
	Bar.Position = UDim2.fromOffset(15, 43)
	Bar.BackgroundColor3 = Color3.fromRGB(55, 25, 60)
	Bar.BorderSizePixel = 0
	Bar.ZIndex = 13
	Bar.Parent = Holder

	local BarCorner = Instance.new("UICorner")
	BarCorner.CornerRadius = UDim.new(1, 0)
	BarCorner.Parent = Bar

	local Fill = Instance.new("Frame")
	Fill.BackgroundColor3 = Pink
	Fill.BorderSizePixel = 0
	Fill.ZIndex = 14
	Fill.Parent = Bar

	local FillCorner = Instance.new("UICorner")
	FillCorner.CornerRadius = UDim.new(1, 0)
	FillCorner.Parent = Fill

	local dragging = false

	local function setValue(value)

		value = math.clamp(
			math.floor(value + 0.5),
			minimum,
			maximum
		)

		local percentage =
			(value - minimum) /
			(maximum - minimum)

		Fill.Size =
			UDim2.new(
				percentage,
				0,
				1,
				0
			)

		ValueLabel.Text = tostring(value)

		callback(value)
	end

	local function mouseValue(x)

		local start = Bar.AbsolutePosition.X
		local width = Bar.AbsoluteSize.X

		local percent =
			math.clamp(
				(x - start) / width,
				0,
				1
			)

		setValue(
			minimum +
			(maximum - minimum) * percent
		)
	end

	Bar.InputBegan:Connect(function(input)

		if input.UserInputType ==
			Enum.UserInputType.MouseButton1 then

			dragging = true
			mouseValue(input.Position.X)
		end
	end)

	UserInputService.InputChanged:Connect(function(input)

		if dragging and
			input.UserInputType ==
			Enum.UserInputType.MouseMovement then

			mouseValue(input.Position.X)
		end
	end)

	UserInputService.InputEnded:Connect(function(input)

		if input.UserInputType ==
			Enum.UserInputType.MouseButton1 then

			dragging = false
		end
	end)

	setValue(default)

	return Holder
end

--========================================================
-- AIM
--========================================================

Section("🎯 AIM ASSIST")

Toggle(
	"Aim Assist • Player Head",
	false,
	function(value)
		Settings.Aim = value
	end
)

Slider(
	"Aim Distance",
	1,
	200,
	200,
	function(value)
		Settings.AimDistance = value
	end
)

Slider(
	"Aim FOV",
	1,
	500,
	120,
	function(value)

		Settings.AimFOV = value

		FOV.Size =
			UDim2.fromOffset(
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

		Settings.AimSmooth =
			value / 100
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

		Settings.AllCheck = value
	end
)

Slider(
	"All Check Distance",
	1,
	3000,
	3000,
	function(value)

		Settings.AllCheckDistance = value
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

		Settings.ESP = value
	end
)

Slider(
	"ESP Distance",
	1,
	3000,
	3000,
	function(value)

		Settings.ESPDistance = value
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

		Settings.Skeleton = value
	end
)

Slider(
	"Skeleton Distance",
	1,
	3000,
	3000,
	function(value)

		Settings.SkeletonDistance = value
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

		Settings.Noclip = value
	end
)

--========================================================
-- PERSONAGENS
--========================================================

local function CharacterOf(player)
	return player.Character
end

local function RootOf(character)
	return character
		and (
			character:FindFirstChild("HumanoidRootPart")
			or character:FindFirstChild("Torso")
		)
end

local function HeadOf(character)
	return character
		and character:FindFirstChild("Head")
end

local function HumanoidOf(character)
	return character
		and character:FindFirstChildOfClass("Humanoid")
end

local function Alive(player)

	local character = CharacterOf(player)
	local humanoid = HumanoidOf(character)

	return character
		and humanoid
		and humanoid.Health > 0
end

local function DistanceFromPlayer(player)

	local myCharacter = LocalPlayer.Character
	local myRoot = RootOf(myCharacter)

	local character = player.Character
	local root = RootOf(character)

	if not myRoot or not root then
		return math.huge
	end

	return (
		myRoot.Position -
		root.Position
	).Magnitude
end

--========================================================
-- VISIBILIDADE / ALL CHECK
--========================================================

local function VisibleTarget(player)

	local character = player.Character
	local head = HeadOf(character)

	if not head then
		return false
	end

	local origin = Camera.CFrame.Position
	local direction = head.Position - origin

	local params = RaycastParams.new()

	params.FilterType =
		Enum.RaycastFilterType.Exclude

	local filter = {
		LocalPlayer.Character
	}

	params.FilterDescendantsInstances = filter

	local result =
		workspace:Raycast(
			origin,
			direction,
			params
		)

	if not result then
		return true
	end

	return result.Instance:IsDescendantOf(character)
end

--========================================================
-- ALL CHECK HIGHLIGHT
--========================================================

local CheckHighlights = {}

local function RemoveCheck(player)

	local highlight =
		CheckHighlights[player]

	if highlight then
		highlight:Destroy()
		CheckHighlights[player] = nil
	end
end

local function UpdateCheck(player)

	if player == LocalPlayer then
		return
	end

	local character = player.Character

	if not character or not Alive(player) then
		RemoveCheck(player)
		return
	end

	local distance =
		DistanceFromPlayer(player)

	if not Settings.AllCheck
		or distance > Settings.AllCheckDistance then

		RemoveCheck(player)
		return
	end

	if not CheckHighlights[player] then

		local highlight =
			Instance.new("Highlight")

		highlight.Name =
			"AllCheckHighlight"

		highlight.Adornee =
			character

		highlight.DepthMode =
			Enum.HighlightDepthMode.AlwaysOnTop

		highlight.Parent =
			character

		CheckHighlights[player] =
			highlight
	end

	local highlight =
		CheckHighlights[player]

	if VisibleTarget(player) then
		highlight.FillColor = Green
		highlight.OutlineColor = Green
	else
		highlight.FillColor = Red
		highlight.OutlineColor = Red
	end

	highlight.FillTransparency = 0.82
	highlight.OutlineTransparency = 0.1
end

--========================================================
-- ESP
--========================================================

local ESPData = {}

local function RemoveESP(player)

	local data = ESPData[player]

	if data then

		if data.Gui then
			data.Gui:Destroy()
		end

		ESPData[player] = nil
	end
end

local function CreateESP(player)

	if ESPData[player] then
		return ESPData[player]
	end

	local character =
		player.Character

	local head =
		HeadOf(character)

	if not head then
		return nil
	end

	local Billboard =
		Instance.new("BillboardGui")

	Billboard.Name =
		"PlayerESP"

	Billboard.Adornee =
		head

	Billboard.Size =
		UDim2.fromOffset(190, 75)

	Billboard.StudsOffset =
		Vector3.new(0, 3, 0)

	Billboard.AlwaysOnTop = true
	Billboard.Enabled = false
	Billboard.Parent = Gui

	local Name =
		Instance.new("TextLabel")

	Name.Size =
		UDim2.new(1, 0, 0, 22)

	Name.BackgroundTransparency = 1
	Name.TextColor3 = Pink
	Name.TextStrokeTransparency = 0.2
	Name.Font = Enum.Font.GothamBold
	Name.TextSize = 14
	Name.Parent = Billboard

	local Info =
		Instance.new("TextLabel")

	Info.Size =
		UDim2.new(1, 0, 0, 20)

	Info.Position =
		UDim2.fromOffset(0, 20)

	Info.BackgroundTransparency = 1
	Info.TextColor3 = White
	Info.TextStrokeTransparency = 0.2
	Info.Font = Enum.Font.Gotham
	Info.TextSize = 11
	Info.Parent = Billboard

	local Back =
		Instance.new("Frame")

	Back.Size =
		UDim2.new(0.75, 0, 0, 7)

	Back.Position =
		UDim2.new(0.125, 0, 0, 47)

	Back.BackgroundColor3 =
		Color3.fromRGB(45, 20, 50)

	Back.BorderSizePixel = 0
	Back.Parent = Billboard

	local BackCorner =
		Instance.new("UICorner")

	BackCorner.CornerRadius =
		UDim.new(1, 0)

	BackCorner.Parent = Back

	local Health =
		Instance.new("Frame")

	Health.Size =
		UDim2.new(1, 0, 1, 0)

	Health.BackgroundColor3 =
		Green

	Health.BorderSizePixel = 0
	Health.Parent = Back

	local HealthCorner =
		Instance.new("UICorner")

	HealthCorner.CornerRadius =
		UDim.new(1, 0)

	HealthCorner.Parent = Health

	ESPData[player] = {
		Gui = Billboard,
		Name = Name,
		Info = Info,
		Health = Health,
	}

	return ESPData[player]
end

local function UpdateESP(player)

	if player == LocalPlayer then
		return
	end

	if not Settings.ESP
		or not Alive(player) then

		RemoveESP(player)
		return
	end

	local distance =
		DistanceFromPlayer(player)

	if distance > Settings.ESPDistance then

		if ESPData[player] then
			ESPData[player].Gui.Enabled = false
		end

		return
	end

	local data =
		CreateESP(player)

	if not data then
		return
	end

	local humanoid =
		HumanoidOf(player.Character)

	data.Gui.Enabled = true

	data.Name.Text =
		player.DisplayName ..
		"  @" ..
		player.Name

	data.Info.Text =
		math.floor(distance) ..
		" studs"

	if humanoid then

		local health =
			math.clamp(
				humanoid.Health /
				math.max(humanoid.MaxHealth, 1),
				0,
				1
			)

		data.Health.Size =
			UDim2.new(
				health,
				0,
				1,
				0
			)

		if health <= 0.25 then
			data.Health.BackgroundColor3 = Red
		elseif health <= 0.5 then
			data.Health.BackgroundColor3 = Yellow
		else
			data.Health.BackgroundColor3 = Green
		end
	end
end

--========================================================
-- SKELETON R6 / R15
--========================================================

local SkeletonData = {}

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

local function ClearSkeleton(player)

	local folder =
		SkeletonData[player]

	if folder then
		folder:Destroy()
		SkeletonData[player] = nil
	end
end

local function Bone(folder, a, b)

	if not a or not b then
		return
	end

	local length =
		(a.Position - b.Position).Magnitude

	if length <= 0 then
		return
	end

	local middle =
		(a.Position + b.Position) / 2

	local part =
		Instance.new("Part")

	part.Name = "Bone"
	part.Anchored = true
	part.CanCollide = false
	part.CanTouch = false
	part.CanQuery = false
	part.Material = Enum.Material.Neon
	part.Color = Pink
	part.Size =
		Vector3.new(
			0.045,
			0.045,
			length
		)

	part.CFrame =
		CFrame.lookAt(
			middle,
			b.Position
		)

	part.Parent = folder
end

local function UpdateSkeleton(player)

	if player == LocalPlayer then
		return
	end

	if not Settings.Skeleton
		or not Alive(player) then

		ClearSkeleton(player)
		return
	end

	local distance =
		DistanceFromPlayer(player)

	if distance > Settings.SkeletonDistance then
		ClearSkeleton(player)
		return
	end

	local character =
		player.Character

	if not character then
		return
	end

	ClearSkeleton(player)

	local folder =
		Instance.new("Folder")

	folder.Name =
		"PlayerSkeleton"

	folder.Parent =
		workspace

	SkeletonData[player] =
		folder

	local humanoid =
		HumanoidOf(character)

	local bones

	if humanoid
		and humanoid.RigType ==
		Enum.HumanoidRigType.R15 then

		bones = R15Bones
	else
		bones = R6Bones
	end

	for _, pair in ipairs(bones) do

		local a =
			character:FindFirstChild(pair[1])

		local b =
			character:FindFirstChild(pair[2])

		Bone(
			folder,
			a,
			b
		)
	end
end

--========================================================
-- TARGET AIM
--========================================================

local function GetTarget()

	local closest = nil
	local closestDistance = math.huge

	local center =
		Vector2.new(
			Camera.ViewportSize.X / 2,
			Camera.ViewportSize.Y / 2
		)

	for _, player in ipairs(Players:GetPlayers()) do

		if player ~= LocalPlayer
			and Alive(player) then

			local distance =
				DistanceFromPlayer(player)

			if distance <= Settings.AimDistance then

				local head =
					HeadOf(player.Character)

				if head then

					local screen,
						onScreen =
						Camera:WorldToViewportPoint(
							head.Position
						)

					if onScreen
						and screen.Z > 0 then

						local point =
							Vector2.new(
								screen.X,
								screen.Y
							)

						local fovDistance =
							(point - center).Magnitude

						if fovDistance <= Settings.AimFOV then

							-- All Check pode exigir
							-- que o alvo esteja visível.
							if not Settings.AllCheck
								or VisibleTarget(player) then

								if fovDistance <
									closestDistance then

									closestDistance =
										fovDistance

									closest =
										player
								end
							end
						end
					end
				end
			end
		end
	end

	return closest
end

--========================================================
-- AIM LOOP
--========================================================

RunService.RenderStepped:Connect(function()

	if not Settings.Aim then
		return
	end

	local character =
		LocalPlayer.Character

	local humanoid =
		HumanoidOf(character)

	if not humanoid
		or humanoid.Health <= 0 then
		return
	end

	local target =
		GetTarget()

	if not target then
		return
	end

	local head =
		HeadOf(target.Character)

	if not head then
		return
	end

	local desired =
		CFrame.lookAt(
			Camera.CFrame.Position,
			head.Position
		)

	Camera.CFrame =
		Camera.CFrame:Lerp(
			desired,
			Settings.AimSmooth
		)
end)

--========================================================
-- ESP / CHECK / SKELETON LOOP
--========================================================

local skeletonTimer = 0

RunService.RenderStepped:Connect(function(delta)

	skeletonTimer += delta

	for _, player in ipairs(Players:GetPlayers()) do

		if player ~= LocalPlayer then

			UpdateCheck(player)
			UpdateESP(player)

			-- Atualização controlada para não
			-- recriar o skeleton centenas de vezes
			if skeletonTimer >= 0.08 then
				UpdateSkeleton(player)
			end
		end
	end

	if skeletonTimer >= 0.08 then
		skeletonTimer = 0
	end
end)

--========================================================
-- NOCLIP
--========================================================

RunService.Stepped:Connect(function()

	if not Settings.Noclip then
		return
	end

	local character =
		LocalPlayer.Character

	if not character then
		return
	end

	for _, object in ipairs(
		character:GetDescendants()
	) do

		if object:IsA("BasePart") then
			object.CanCollide = false
		end
	end
end)

--========================================================
-- PLAYER ADDED / REMOVED
--========================================================

local function SetupPlayer(player)

	if player == LocalPlayer then
		return
	end

	player.CharacterRemoving:Connect(function()

		RemoveESP(player)
		RemoveCheck(player)
		ClearSkeleton(player)
	end)
end

for _, player in ipairs(
	Players:GetPlayers()
) do
	SetupPlayer(player)
end

Players.PlayerAdded:Connect(SetupPlayer)

Players.PlayerRemoving:Connect(function(player)

	RemoveESP(player)
	RemoveCheck(player)
	ClearSkeleton(player)
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

		input.Changed:Connect(function()

			if input.UserInputState ==
				Enum.UserInputState.End then

				dragging = false
			end
		end)
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

	if input.KeyCode ==
		Enum.KeyCode.RightShift then

		Settings.Panel =
			not Settings.Panel

		Main.Visible =
			Settings.Panel
	end
end)

--========================================================
-- ATUALIZAÇÃO DO FOV
--========================================================

RunService.RenderStepped:Connect(function()

	FOV.Position =
		UDim2.fromOffset(
			Camera.ViewportSize.X / 2,
			Camera.ViewportSize.Y / 2
		)
end)

print("========================================")
print(" NEON PLAYER SYSTEM")
print(" R6 / R15")
print(" AIM / ALL CHECK / ESP / SKELETON")
print(" RIGHTSHIFT = PAINEL")
print("========================================")
