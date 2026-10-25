local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera

local LP = Players.LocalPlayer

local GUI = Instance.new("ScreenGui")
GUI.Name = "MM2_TestHub"
GUI.ResetOnSpawn = false
GUI.Parent = LP:WaitForChild("PlayerGui")

local Ball = Instance.new("ImageButton")
Ball.Size = UDim2.fromOffset(60,60)
Ball.Position = UDim2.new(0,20,0.5,-30)
Ball.BackgroundTransparency = 1
Ball.Image = "rbxassetid://9452307933"
Ball.Parent = GUI

local Menu = Instance.new("Frame")
Menu.Size = UDim2.fromOffset(300,470)
Menu.Position = UDim2.new(0.5,-150,0.5,-235)
Menu.BackgroundColor3 = Color3.fromRGB(18,18,25)
Menu.BorderSizePixel = 0
Menu.Visible = false
Menu.Active = true
Menu.Parent = GUI

local MC = Instance.new("UICorner")
MC.CornerRadius = UDim.new(0,16)
MC.Parent = Menu

local MS = Instance.new("UIStroke")
MS.Thickness = 3
MS.Parent = Menu

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1,-20,0,45)
Title.Position = UDim2.fromOffset(10,5)
Title.BackgroundTransparency = 1
Title.Text = "MM2 TEST HUB"
Title.TextScaled = true
Title.Font = Enum.Font.GothamBold
Title.Parent = Menu

local Holder = Instance.new("ScrollingFrame")
Holder.Size = UDim2.new(1,-20,1,-60)
Holder.Position = UDim2.fromOffset(10,55)
Holder.BackgroundTransparency = 1
Holder.BorderSizePixel = 0
Holder.ScrollBarThickness = 4
Holder.CanvasSize = UDim2.new(0,0,0,0)
Holder.Parent = Menu

local Layout = Instance.new("UIListLayout")
Layout.Padding = UDim.new(0,7)
Layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
Layout.Parent = Holder

Layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	Holder.CanvasSize = UDim2.new(0,0,0,Layout.AbsoluteContentSize.Y+10)
end)

local function NewButton(Text)
	local B = Instance.new("TextButton")
	B.Size = UDim2.new(1,-4,0,43)
	B.BackgroundColor3 = Color3.fromRGB(35,35,48)
	B.Text = Text
	B.TextColor3 = Color3.new(1,1,1)
	B.TextScaled = true
	B.Font = Enum.Font.GothamBold
	B.Parent = Holder

	local C = Instance.new("UICorner")
	C.CornerRadius = UDim.new(0,10)
	C.Parent = B

	return B
end

local CoinButton = NewButton("🪙 AUTO COIN: OFF")
local MurderButton = NewButton("🔪 BOX MURDERER: OFF")
local SheriffButton = NewButton("🔫 BOX SHERIFF: OFF")
local InnocentButton = NewButton("👤 BOX INNOCENT: OFF")
local AimButton = NewButton("🎯 AIMBOT MURDERER: OFF")
local FOVButton = NewButton("⭕ FOV AIM: 100")
local TPCoinButton = NewButton("🔫 TP ARMA")
local TPSheriffButton = NewButton("🔫 TP SHERIFF")
local TPMurdererButton = NewButton("🔪 TP ATRÁS MURDERER")
local TPMapButton = NewButton("🏠 TP EM CIMA DO MAPA")

local Hue = 0
local FOV = 100
local AimBot = false

RunService.RenderStepped:Connect(function(dt)
	Hue = (Hue + dt * 0.18) % 1
	local Color = Color3.fromHSV(Hue,1,1)

	MS.Color = Color
	Title.TextColor3 = Color

	Menu.Position = UDim2.new(
		0.5,
		-150 + math.sin(time()*1.5)*8,
		0.5,
		-235 + math.cos(time()*1.2)*6
	)

	Ball.Rotation = math.sin(time()*2)*8

	if AimBot then
		local Target
		local Distance = math.huge

		for _,Player in ipairs(Players:GetPlayers()) do
			if Player ~= LP and Player.Character then
				local Role = Player:GetAttribute("Role")

				if Role == "Murderer" then
					local Humanoid = Player.Character:FindFirstChildOfClass("Humanoid")
					local Root = Player.Character:FindFirstChild("HumanoidRootPart")

					if Humanoid and Root and Humanoid.Health > 0 then
						local ScreenPos,Visible = Camera:WorldToViewportPoint(Root.Position)

						if Visible then
							local Center = Vector2.new(
								Camera.ViewportSize.X/2,
								Camera.ViewportSize.Y/2
							)

							local Dist = (
								Vector2.new(ScreenPos.X,ScreenPos.Y)-Center
							).Magnitude

							if Dist <= FOV and Dist < Distance then
								Distance = Dist
								Target = Root
							end
						end
					end
				end
			end
		end

		if Target then
			Camera.CFrame = CFrame.lookAt(
				Camera.CFrame.Position,
				Target.Position
			)
		end
	end
end)

Ball.MouseButton1Click:Connect(function()
	Menu.Visible = not Menu.Visible
end)

local Dragging = false
local DragStart
local StartPosition

Ball.InputBegan:Connect(function(Input)
	if Input.UserInputType == Enum.UserInputType.MouseButton1
	or Input.UserInputType == Enum.UserInputType.Touch then
		Dragging = true
		DragStart = Input.Position
		StartPosition = Ball.Position
	end
end)

Ball.InputEnded:Connect(function(Input)
	if Input.UserInputType == Enum.UserInputType.MouseButton1
	or Input.UserInputType == Enum.UserInputType.Touch then
		Dragging = false
	end
end)

UIS.InputChanged:Connect(function(Input)
	if not Dragging then return end

	if Input.UserInputType == Enum.UserInputType.MouseMovement
	or Input.UserInputType == Enum.UserInputType.Touch then

		local Delta = Input.Position - DragStart

		Ball.Position = UDim2.new(
			StartPosition.X.Scale,
			StartPosition.X.Offset + Delta.X,
			StartPosition.Y.Scale,
			StartPosition.Y.Offset + Delta.Y
		)
	end
end)

local function GetRole(Player)
	local Role = Player:GetAttribute("Role")

	if Role then
		return Role
	end

	if Player.Character then
		local V = Player.Character:FindFirstChild("Role")

		if V and V:IsA("StringValue") then
			return V.Value
		end
	end
	
	local Backpack = Player:FindFirstChild("Backpack")
	local Character = Player.Character
	
	local hasKnife = (Backpack and Backpack:FindFirstChild("Knife")) or (Character and Character:FindFirstChild("Knife"))
	local hasGun = (Backpack and Backpack:FindFirstChild("Gun")) or (Character and Character:FindFirstChild("Gun")) or (Backpack and Backpack:FindFirstChild("Revolver")) or (Character and Character:FindFirstChild("Revolver"))
	
	if hasKnife then
		return "Murderer"
	elseif hasGun then
		return "Sheriff"
	else
		return "Innocent"
	end
end

local ESP = {
	Murderer = false,
	Sheriff = false,
	Innocent = false
}

local ESPObjects = {}

local function ClearESP()
	for _,Object in pairs(ESPObjects) do
		if Object then
			Object:Destroy()
		end
	end

	table.clear(ESPObjects)
end

local function UpdateESP()
	ClearESP()

	for _,Player in ipairs(Players:GetPlayers()) do
		if Player ~= LP and Player.Character then

			local Role = GetRole(Player)
			local Color

			if Role == "Murderer" and ESP.Murderer then
				Color = Color3.fromRGB(255,0,0)

			elseif Role == "Sheriff" and ESP.Sheriff then
				Color = Color3.fromRGB(0,150,255)

			elseif Role == "Innocent" and ESP.Innocent then
				Color = Color3.fromRGB(0,255,80)
			end

			if Color then
				local Highlight = Instance.new("Highlight")
				Highlight.Name = "ESP_BOX"
				Highlight.Adornee = Player.Character
				Highlight.FillColor = Color
				Highlight.OutlineColor = Color
				Highlight.FillTransparency = 0.65
				Highlight.OutlineTransparency = 0
				Highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
				Highlight.Parent = Player.Character

				ESPObjects[Player] = Highlight
			end
		end
	end
end

MurderButton.MouseButton1Click:Connect(function()
	ESP.Murderer = not ESP.Murderer

	MurderButton.Text =
		ESP.Murderer and "🔪 BOX MURDERER: ON"
		or "🔪 BOX MURDERER: OFF"

	UpdateESP()
end)

SheriffButton.MouseButton1Click:Connect(function()
	ESP.Sheriff = not ESP.Sheriff

	SheriffButton.Text =
		ESP.Sheriff and "🔫 BOX SHERIFF: ON"
		or "🔫 BOX SHERIFF: OFF"

	UpdateESP()
end)

InnocentButton.MouseButton1Click:Connect(function()
	ESP.Innocent = not ESP.Innocent

	InnocentButton.Text =
		ESP.Innocent and "👤 BOX INNOCENT: ON"
		or "👤 BOX INNOCENT: OFF"

	UpdateESP()
end)

AimButton.MouseButton1Click:Connect(function()
	AimBot = not AimBot

	AimButton.Text =
		AimBot and "🎯 AIMBOT MURDERER: ON"
		or "🎯 AIMBOT MURDERER: OFF"
end)

FOVButton.MouseButton1Click:Connect(function()
	FOV += 25

	if FOV > 250 then
		FOV = 50
	end

	FOVButton.Text = "⭕ FOV AIM: "..FOV
end)

local function GetRoot()
	local Character = LP.Character
	return Character and Character:FindFirstChild("HumanoidRootPart")
end

local function GetPlayerByRole(Role)
	for _,Player in ipairs(Players:GetPlayers()) do
		if Player ~= LP and GetRole(Player) == Role then
			if Player.Character then
				local Humanoid = Player.Character:FindFirstChildOfClass("Humanoid")
				local Root = Player.Character:FindFirstChild("HumanoidRootPart")

				if Root and (not Humanoid or Humanoid.Health > 0) then
					return Player
				end
			end
		end
	end
end

local function FindGun()
	for _,Object in ipairs(workspace:GetDescendants()) do
		if Object:IsA("BasePart") or Object:IsA("Model") then
			local Name = string.lower(Object.Name)

			if Name == "gundrop"
			or Name == "gun"
			or Name == "revolver" then
				if Object:IsA("Model") then
					return Object.PrimaryPart or Object:FindFirstChildWhichIsA("BasePart")
				else
					return Object
				end
			end
		end
	end
end

TPCoinButton.MouseButton1Click:Connect(function()
	local Root = GetRoot()
	local Gun = FindGun()

	if Root and Gun then
		Root.CFrame = Gun.CFrame + Vector3.new(0,3,0)
	end
end)

TPSheriffButton.MouseButton1Click:Connect(function()
	local Root = GetRoot()
	local Sheriff = GetPlayerByRole("Sheriff")

	if Root and Sheriff and Sheriff.Character then
		local Target = Sheriff.Character:FindFirstChild("HumanoidRootPart")

		if Target then
			Root.CFrame = Target.CFrame + Vector3.new(0,3,0)
		end
	end
end)

TPMurdererButton.MouseButton1Click:Connect(function()
	local Root = GetRoot()
	local Murderer = GetPlayerByRole("Murderer")

	if Root and Murderer and Murderer.Character then
		local Target = Murderer.Character:FindFirstChild("HumanoidRootPart")

		if Target then
			Root.CFrame =
				Target.CFrame
				* CFrame.new(0,0,5)
		end
	end
end)

local function GetHighestMapPoint()
	local Highest
	local HighestY = -math.huge

	for _,Object in ipairs(workspace:GetDescendants()) do
		if Object:IsA("BasePart")
		and Object.Transparency < 1
		and Object.CanCollide
		and Object.Size.X > 8
		and Object.Size.Z > 8 then

			local TopY = Object.Position.Y + Object.Size.Y/2

			if TopY > HighestY then
				HighestY = TopY
				Highest = Object
			end
		end
	end

	return Highest
end

TPMapButton.MouseButton1Click:Connect(function()
	local Root = GetRoot()
	local Map = GetHighestMapPoint()

	if Root and Map then
		Root.CFrame = CFrame.new(
			Map.Position.X,
			Map.Position.Y + Map.Size.Y/2 + 8,
			Map.Position.Z
		)
	end
end)

local AutoCoin = false

CoinButton.MouseButton1Click:Connect(function()
	AutoCoin = not AutoCoin

	CoinButton.Text =
		AutoCoin and "🪙 AUTO COIN: ON"
		or "🪙 AUTO COIN: OFF"
end)

local function FindCoin()
	for _,Object in ipairs(workspace:GetDescendants()) do
		if Object:IsA("BasePart") or Object:IsA("Model") then
			local Name = string.lower(Object.Name)

			if Name == "coin"
			or Name == "coincontainer"
			or Name == "coinbag"
			or Name == "cash" then
				if Object:IsA("Model") then
					return Object.PrimaryPart or Object:FindFirstChildWhichIsA("BasePart")
				else
					return Object
				end
			end
		end
	end
end

task.spawn(function()
	while task.wait(0.25) do
		if AutoCoin then
			local Root = GetRoot()
			local Coin = FindCoin()

			if Root and Coin then
				Root.CFrame = Coin.CFrame + Vector3.new(0,3,0)
			end
		end
	end
end)

task.spawn(function()
	while task.wait(0.3) do
		if ESP.Murderer or ESP.Sheriff or ESP.Innocent then
			UpdateESP()
		end
	end
end)

Players.PlayerRemoving:Connect(function(Player)
	if ESPObjects[Player] then
		ESPObjects[Player]:Destroy()
		ESPObjects[Player] = nil
	end
end)

