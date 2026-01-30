-- Serviços
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local p = Players.LocalPlayer

-- Funções úteis
local function char()
	return p.Character or p.CharacterAdded:Wait()
end

local function hum()
	return char():WaitForChild("Humanoid")
end

--------------------------------------------------
-- GUI
--------------------------------------------------
local g = Instance.new("ScreenGui")
g.Name = "MobileHackMenu"
g.ResetOnSpawn = false
g.Parent = p:WaitForChild("PlayerGui")

--------------------------------------------------
-- BOLINHA
--------------------------------------------------
local ball = Instance.new("TextButton")
ball.Size = UDim2.new(0,55,0,55)
ball.Position = UDim2.new(0,15,0.6,0)
ball.Text = "⚡"
ball.BackgroundColor3 = Color3.fromRGB(140,0,200)
ball.TextColor3 = Color3.new(1,1,1)
ball.Font = Enum.Font.GothamBold
ball.TextSize = 24
ball.BorderSizePixel = 0
ball.ZIndex = 10
ball.Parent = g

local bc = Instance.new("UICorner")
bc.CornerRadius = UDim.new(1,0)
bc.Parent = ball

--------------------------------------------------
-- MENU
--------------------------------------------------
local menu = Instance.new("Frame")
menu.Size = UDim2.new(0,300,0,280)
menu.Position = UDim2.new(0.5,-150,0.5,-140)
menu.BackgroundColor3 = Color3.fromRGB(18,18,18)
menu.BorderColor3 = Color3.fromRGB(160,0,255)
menu.BorderSizePixel = 2
menu.Visible = false
menu.Parent = g

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1,0,0,35)
title.Text = "⚡ MENU ⚡"
title.BackgroundColor3 = Color3.fromRGB(25,25,25)
title.TextColor3 = Color3.fromRGB(180,0,255)
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.Parent = menu

local min = Instance.new("TextButton")
min.Size = UDim2.new(0,30,0,30)
min.Position = UDim2.new(1,-35,0,2)
min.Text = "-"
min.BackgroundColor3 = Color3.fromRGB(40,40,40)
min.TextColor3 = Color3.new(1,1,1)
min.Font = Enum.Font.GothamBold
min.TextSize = 20
min.Parent = menu

--------------------------------------------------
-- FUNÇÃO BOTÃO
--------------------------------------------------
local function makeBtn(text,y,color)
	local b = Instance.new("TextButton")
	b.Size = UDim2.new(0,260,0,40)
	b.Position = UDim2.new(0,20,0,y)
	b.Text = text
	b.BackgroundColor3 = color
	b.TextColor3 = Color3.new(1,1,1)
	b.Font = Enum.Font.GothamBold
	b.TextSize = 16
	b.Parent = menu
	return b
end

local godBtn = makeBtn("IMORTAL: OFF",60,Color3.fromRGB(60,0,0))
local speedBtn = makeBtn("SPEED: OFF",115,Color3.fromRGB(60,0,0))
local dupBtn = makeBtn("DUPLICAR ITEM",170,Color3.fromRGB(40,40,40))

--------------------------------------------------
-- ABRIR / FECHAR MENU
--------------------------------------------------
ball.InputBegan:Connect(function(i)
	if i.UserInputType == Enum.UserInputType.Touch
	or i.UserInputType == Enum.UserInputType.MouseButton1 then
		menu.Visible = not menu.Visible
	end
end)

--------------------------------------------------
-- ARRASTAR BOLINHA
--------------------------------------------------
do
	local drag,startPos,startUI
	ball.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.Touch
		or i.UserInputType == Enum.UserInputType.MouseButton1 then
			drag = true
			startPos = i.Position
			startUI = ball.Position
		end
	end)
	ball.InputEnded:Connect(function() drag = false end)
	UIS.InputChanged:Connect(function(i)
		if drag and (i.UserInputType == Enum.UserInputType.Touch
		or i.UserInputType == Enum.UserInputType.MouseMovement) then
			local d = i.Position - startPos
			ball.Position = UDim2.new(
				startUI.X.Scale,startUI.X.Offset+d.X,
				startUI.Y.Scale,startUI.Y.Offset+d.Y
			)
		end
	end)
end

--------------------------------------------------
-- ARRASTAR MENU
--------------------------------------------------
do
	local drag,startPos,startUI
	title.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.Touch
		or i.UserInputType == Enum.UserInputType.MouseButton1 then
			drag = true
			startPos = i.Position
			startUI = menu.Position
		end
	end)
	title.InputEnded:Connect(function() drag = false end)
	UIS.InputChanged:Connect(function(i)
		if drag and (i.UserInputType == Enum.UserInputType.Touch
		or i.UserInputType == Enum.UserInputType.MouseMovement) then
			local d = i.Position - startPos
			menu.Position = UDim2.new(
				startUI.X.Scale,startUI.X.Offset+d.X,
				startUI.Y.Scale,startUI.Y.Offset+d.Y
			)
		end
	end)
end

--------------------------------------------------
-- IMORTAL REFORÇADO
--------------------------------------------------
local god = false
local godLoop

godBtn.MouseButton1Click:Connect(function()
	god = not god
	local h = hum()

	if god then
		godBtn.Text = "IMORTAL: ON"
		godBtn.BackgroundColor3 = Color3.fromRGB(120,0,180)

		if godLoop then godLoop:Disconnect() end
		godLoop = RunService.Heartbeat:Connect(function()
			if h and h.Health < h.MaxHealth then
				h.MaxHealth = math.huge
				h.Health = h.MaxHealth
			end
		end)
	else
		godBtn.Text = "IMORTAL: OFF"
		godBtn.BackgroundColor3 = Color3.fromRGB(60,0,0)
		if godLoop then godLoop:Disconnect() end
		h.MaxHealth = 100
		h.Health = 100
	end
end)

--------------------------------------------------
-- SPEED (CLIENT-SIDE)
--------------------------------------------------
local speed = false
local normal = 16
local fast = 50
local speedLoop

speedBtn.MouseButton1Click:Connect(function()
	speed = not speed
	local h = hum()

	if speed then
		speedBtn.Text = "SPEED: ON"
		speedBtn.BackgroundColor3 = Color3.fromRGB(0,140,200)

		if speedLoop then speedLoop:Disconnect() end
		speedLoop = RunService.Heartbeat:Connect(function()
			if h then h.WalkSpeed = fast end
		end)
	else
		speedBtn.Text = "SPEED: OFF"
		speedBtn.BackgroundColor3 = Color3.fromRGB(60,0,0)
		if speedLoop then speedLoop:Disconnect() end
		h.WalkSpeed = normal
	end
end)

--------------------------------------------------
-- DUPLICAR ITEM (PERSISTENTE NO CLIENTE)
--------------------------------------------------
local savedTools = {}

dupBtn.MouseButton1Click:Connect(function()
	local tool = char():FindFirstChildOfClass("Tool")
	if not tool then
		dupBtn.Text = "SEM ITEM ❌"
		task.delay(0.8,function() dupBtn.Text="DUPLICAR ITEM" end)
		return
	end

	local clone = tool:Clone()
	clone.Parent = p.Backpack
	table.insert(savedTools, clone.Name)

	dupBtn.Text = "DUPLICADO ✔"
	task.delay(0.8,function() dupBtn.Text="DUPLICAR ITEM" end)
end)

-- reaplica ao morrer
p.CharacterAdded:Connect(function()
	task.wait(1)
	local h = hum()

	if god then
		h.MaxHealth = math.huge
		h.Health = h.MaxHealth
	end
	if speed then
		h.WalkSpeed = fast
	end

	for _,name in ipairs(savedTools) do
		if not p.Backpack:FindFirstChild(name) then
			local t = game:GetService("ReplicatedStorage"):FindFirstChild(name,true)
			if t then t:Clone().Parent = p.Backpack end
		end
	end
end)

--------------------------------------------------
-- MINIMIZAR
--------------------------------------------------
local closed = false
local fullSize = menu.Size

min.MouseButton1Click:Connect(function()
	closed = not closed
	if closed then
		min.Text = "+"
		for _,v in pairs(menu:GetChildren()) do
			if v:IsA("TextButton") and v ~= min then
				v.Visible = false
			end
		end
		menu.Size = UDim2.new(fullSize.X.Scale,fullSize.X.Offset,0,35)
	else
		min.Text = "-"
		for _,v in pairs(menu:GetChildren()) do
			if v:IsA("TextButton") then
				v.Visible = true
			end
		end
		menu.Size = fullSize
	end
end)
