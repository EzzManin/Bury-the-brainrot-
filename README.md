--// =========================
--// EzzHub - LocalScript
--// =========================

-- Remover instância antiga
pcall(function()
    if game.CoreGui:FindFirstChild("EzzHub") then
        game.CoreGui.EzzHub:Destroy()
    end
end)

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")

-- Função para pegar Character
local function getChar()
    local c = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    return c, c:WaitForChild("HumanoidRootPart")
end

-- GUI principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "EzzHub"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game.CoreGui

-- Botão de abrir/fechar
local OpenBtn = Instance.new("ImageButton")
OpenBtn.Name = "OpenBtn"
OpenBtn.Size = UDim2.new(0, 50, 0, 50)
OpenBtn.Position = UDim2.new(0, 20, 0, 200)
OpenBtn.Image = "rbxassetid://133393242441626"
OpenBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
OpenBtn.Parent = ScreenGui

-- Frame principal
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 250, 0, 300)
MainFrame.Position = UDim2.new(0.5, -125, 0.5, -150)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.Visible = false
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 30)
Title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
Title.Text = "EzzHub"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Parent = MainFrame

-- Variáveis para locais salvos
local savedPositions = {}
local currentPosIndex = 1

-- Função criar botão
local function createBtn(name, text, yPos)
    local btn = Instance.new("TextButton")
    btn.Name = name
    btn.Size = UDim2.new(0, 200, 0, 35)
    btn.Position = UDim2.new(0.5, -100, 0, yPos)
    btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Text = text
    btn.Parent = MainFrame
    return btn
end

-- Botões principais
local SaveBtn = createBtn("SaveBtn", "Salvar Local", 50)
local TpBtn = createBtn("TpBtn", "Teleportar Local", 95)
local DashBtn = createBtn("DashBtn", "Dash Frente", 140)

-- Popup para confirmação
local Popup = Instance.new("Frame")
Popup.Size = UDim2.new(0, 200, 0, 120)
Popup.Position = UDim2.new(0.5, -100, 0.5, -60)
Popup.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Popup.Visible = false
Popup.Parent = MainFrame

local PopupText = Instance.new("TextLabel")
PopupText.Size = UDim2.new(1, 0, 0, 40)
PopupText.Text = "Você deseja salvar outro lugar?"
PopupText.TextColor3 = Color3.fromRGB(255,255,255)
PopupText.BackgroundTransparency = 1
PopupText.Parent = Popup

local YesBtn = Instance.new("TextButton")
YesBtn.Size = UDim2.new(0.5, -5, 0, 40)
YesBtn.Position = UDim2.new(0, 0, 0, 60)
YesBtn.Text = "Sim"
YesBtn.BackgroundColor3 = Color3.fromRGB(60,60,60)
YesBtn.TextColor3 = Color3.fromRGB(255,255,255)
YesBtn.Parent = Popup

local NoBtn = Instance.new("TextButton")
NoBtn.Size = UDim2.new(0.5, -5, 0, 40)
NoBtn.Position = UDim2.new(0.5, 5, 0, 60)
NoBtn.Text = "Não"
NoBtn.BackgroundColor3 = Color3.fromRGB(60,60,60)
NoBtn.TextColor3 = Color3.fromRGB(255,255,255)
NoBtn.Parent = Popup

-- Lógica abrir/fechar menu
OpenBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

-- Função Salvar
SaveBtn.MouseButton1Click:Connect(function()
    if #savedPositions > 0 then
        Popup.Visible = true
    else
        local _, hrp = getChar()
        table.insert(savedPositions, hrp.Position)
        currentPosIndex = #savedPositions
    end
end)

-- Popup Sim/Não
YesBtn.MouseButton1Click:Connect(function()
    local _, hrp = getChar()
    table.insert(savedPositions, hrp.Position)
    currentPosIndex = #savedPositions
    Popup.Visible = false
end)

NoBtn.MouseButton1Click:Connect(function()
    Popup.Visible = false
end)

-- Teleportar
TpBtn.MouseButton1Click:Connect(function()
    if savedPositions[currentPosIndex] then
        local _, hrp = getChar()
        hrp.CFrame = CFrame.new(savedPositions[currentPosIndex])
    end
end)

-- Dash
DashBtn.MouseButton1Click:Connect(function()
    local char, hrp = getChar()
    local look = hrp.CFrame.LookVector
    local dashDist = 20
    local targetPos = hrp.Position + (look * dashDist)

    local tween = TweenService:Create(
        hrp,
        TweenInfo.new(0.3, Enum.EasingStyle.Linear),
        {CFrame = CFrame.new(targetPos)}
    )
    tween:Play()
end)

-- Tornar botão openBtn arrastável
OpenBtn.Active = true
OpenBtn.Draggable = true
