-- =========================
-- SERVIÇOS
-- =========================
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualUser = game:GetService("VirtualUser")
local CoreGui = game:GetService("CoreGui")

local player = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- =========================
-- ESTADOS DAS HABILIDADES
-- =========================
local TeleportON = false
local AfkON = false
local ClickON = false

-- =========================
-- GUI
-- =========================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "DiamondPanel_Delta"
ScreenGui.Parent = CoreGui
ScreenGui.ResetOnSpawn = false

-- Botão abrir
local OpenButton = Instance.new("ImageButton", ScreenGui)
OpenButton.Size = UDim2.new(0,50,0,50)
OpenButton.Position = UDim2.new(0.02,0,0.5,0)
OpenButton.Image = "rbxassetid://6015132441"
OpenButton.Visible = false
OpenButton.BackgroundTransparency = 0.3
Instance.new("UICorner", OpenButton).CornerRadius = UDim.new(1,0)

-- Painel principal
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0,250,0,250)
MainFrame.Position = UDim2.new(0.5,-125,0.4,-125)
MainFrame.BackgroundColor3 = Color3.fromRGB(64,224,208)
MainFrame.BorderSizePixel = 0
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0,20)

-- Título
local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(0.7,0,0.15,0)
Title.Position = UDim2.new(0.1,0,0.05,0)
Title.BackgroundTransparency = 1
Title.Text = "PAINEL DIAMANTE"
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 18
Title.TextColor3 = Color3.new(0,0,0)

-- Minimizar
local MinimizeBtn = Instance.new("TextButton", MainFrame)
MinimizeBtn.Size = UDim2.new(0.15,0,0.15,0)
MinimizeBtn.Position = UDim2.new(0.8,0,0.05,0)
MinimizeBtn.Text = "—"
MinimizeBtn.TextSize = 24
MinimizeBtn.BackgroundTransparency = 1

-- Container
local ButtonContainer = Instance.new("Frame", MainFrame)
ButtonContainer.Size = UDim2.new(0.8,0,0.6,0)
ButtonContainer.Position = UDim2.new(0.1,0,0.3,0)
ButtonContainer.BackgroundTransparency = 1

local Layout = Instance.new("UIListLayout", ButtonContainer)
Layout.Padding = UDim.new(0,10)
Layout.HorizontalAlignment = Enum.HorizontalAlignment.Center

-- =========================
-- FUNÇÕES AUXILIARES
-- =========================
local function getSize(plr)
    local stats = plr:FindFirstChild("leaderstats")
    if stats then
        local v = stats:FindFirstChild("Tamanho") or stats:FindFirstChild("Size") or stats:FindFirstChild("Clicks") or stats:FindFirstChild("Power")
        if v then return v.Value end
    end
    return 0
end

-- =========================
-- BOTÃO 1 - TELEPORT
-- =========================
local Btn1 = Instance.new("TextButton", ButtonContainer)
Btn1.Size = UDim2.new(1,0,0,35)
Btn1.Text = "TELEPORT: OFF"
Btn1.BackgroundColor3 = Color3.fromRGB(150,0,0)
Btn1.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", Btn1).CornerRadius = UDim.new(0,8)

task.spawn(function()
    while true do
        if TeleportON and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local mySize = getSize(player)
            for _,p in pairs(Players:GetPlayers()) do
                if TeleportON and p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                    if getSize(p) < mySize then
                        player.Character.HumanoidRootPart.CFrame =
                            p.Character.HumanoidRootPart.CFrame * CFrame.new(0,2,0)
                        task.wait(0.3)
                    end
                end
            end
        end
        task.wait(0.1)
    end
end)

Btn1.MouseButton1Click:Connect(function()
    TeleportON = not TeleportON
    Btn1.Text = TeleportON and "TELEPORT: ON" or "TELEPORT: OFF"
    Btn1.BackgroundColor3 = TeleportON and Color3.fromRGB(0,150,0) or Color3.fromRGB(150,0,0)
end)

-- =========================
-- BOTÃO 2 - AFK INTELIGENTE
-- =========================
local Btn2 = Instance.new("TextButton", ButtonContainer)
Btn2.Size = UDim2.new(0.9,0,0,35)
Btn2.Text = "AFK: OFF"
Btn2.BackgroundColor3 = Color3.fromRGB(150,0,0)
Btn2.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", Btn2)

local VELOCIDADE_FUGA = 100
local RAIO = 100
local velocidadeOriginal = 16

task.spawn(function()
    while true do
        if AfkON and player.Character and player.Character:FindFirstChild("Humanoid") then
            local hum = player.Character.Humanoid
            local pos = player.Character.HumanoidRootPart.Position
            local mySize = getSize(player)
            local perigo = false

            for _,p in pairs(Players:GetPlayers()) do
                if p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                    local dist = (pos - p.Character.HumanoidRootPart.Position).Magnitude
                    if getSize(p) > mySize and dist < RAIO then
                        perigo = true
                        hum.WalkSpeed = VELOCIDADE_FUGA
                        hum:MoveTo(pos + (pos - p.Character.HumanoidRootPart.Position).Unit * 30)
                    end
                end
            end

            if not perigo then hum.WalkSpeed = velocidadeOriginal end
        end
        task.wait(0.1)
    end
end)

Btn2.MouseButton1Click:Connect(function()
    AfkON = not AfkON
    Btn2.Text = AfkON and "AFK: ON" or "AFK: OFF"
    Btn2.BackgroundColor3 = AfkON and Color3.fromRGB(0,150,0) or Color3.fromRGB(150,0,0)
end)

-- =========================
-- BOTÃO 3 - AUTO CLICKS
-- =========================
local Btn3 = Instance.new("TextButton", ButtonContainer)
Btn3.Size = UDim2.new(0.8,0,0,35)
Btn3.Text = "AUTO CLICK: OFF"
Btn3.BackgroundColor3 = Color3.fromRGB(150,0,0)
Btn3.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", Btn3)

local function startClicks()
    for i=1,5 do
        task.spawn(function()
            while ClickON do
                VirtualUser:CaptureController()
                VirtualUser:ClickButton1(Vector2.new(999,999))
                task.wait(0.001)
            end
        end)
    end
end

Btn3.MouseButton1Click:Connect(function()
    ClickON = not ClickON
    Btn3.Text = ClickON and "AUTO CLICK: ON" or "AUTO CLICK: OFF"
    Btn3.BackgroundColor3 = ClickON and Color3.fromRGB(0,150,0) or Color3.fromRGB(150,0,0)
    if ClickON then startClicks() end
end)

-- =========================
-- MINIMIZAR / ABRIR
-- =========================
MinimizeBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = false
    OpenButton.Visible = true
end)

OpenButton.MouseButton1Click:Connect(function()
    OpenButton.Visible = false
    MainFrame.Visible = true
end)

-- =========================
-- ARRASTAR PAINEL
-- =========================
local dragging, dragStart, startPos
MainFrame.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = i.Position
        startPos = MainFrame.Position
        i.Changed:Connect(function()
            if i.UserInputState == Enum.UserInputState.End then dragging = false end
        end)
    end
end)

UserInputService.InputChanged:Connect(function(i)
    if dragging and (i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseMovement) then
        local delta = i.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X,
                                      startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)# Um-clique-aumente-seu-tamanho-script
