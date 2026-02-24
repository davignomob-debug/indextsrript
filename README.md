local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

local sg = Instance.new("ScreenGui", (gethui and gethui()) or game:GetService("CoreGui"))
sg.Name = "AutoFarm_V2"

local Brainrots = {
    "Tim Cheese", "LiriliLarila", "Fluri Flura", "Cacto Hipopotamo",
    "Pipi Potato", "Tric Trac Barabum", "Burbaloni Loliloli", "Boneca Ambalabu",
    "Trippi Troppi", "Svinina Bombardino", "Bambini Crostini", "Avocadini Guffo",
    "Bandito Bobrito", "Tatatata Sahur",
    "Gangster Footera", "Tung Tung Sahur", "Cappuccino Assassino", "Pipi Kiwi",
    "Orangutini Ananassini", "Talpa Di Fero", "Espresso Signora",
    "Brr Brr Patapim", "Rhino Toasterino", "Brr Bicus Dicus",
    "Strawberrelli Flamingelli", "Bananito Delfinito", "Balerina Capucina", "Glorbo Fruttodrillo",
    "Blueberrinni Octopusini", "Chimpanzini Bananini", "Bombardiro Crocodilo", "Elefanto Cocofanto",
    "Bombombini Gusini", "Pandaccini Bananini", "Chef Crabracadabra",
    "Gorillo Watermelondrillo", "Frigo Camelo", "Girafa Celestre",
    "Ganganzelli Trulala", "Tigroligre Frutonni",
    "Tralalerodon", "Esok", "La Vaca", "Strawberry",
    "Capitano Clash Warnini", "Meowl", "Garama & Madundung"
}

local AlvosSelecionados = {}
local FarmAtivo = false
local ValorMinimo = 0

local dragging = false
local dragInput = nil
local dragStart = nil
local startPos = nil

local function ParseValorPS(texto)
    if not texto then return 0 end
    local s = texto:upper():gsub("%s", ""):gsub("%$", ""):gsub("/S", "")
    local num, sufixo = s:match("([%d%.]+)([KMB]?)")
    if not num then return 0 end
    num = tonumber(num) or 0
    if sufixo == "K" then num = num * 1000
    elseif sufixo == "M" then num = num * 1000000
    elseif sufixo == "B" then num = num * 1000000000
    end
    return num
end

local function ParseInput(texto)
    if not texto or texto == "" then return 0 end
    local s = texto:upper():gsub("%s", ""):gsub("%$", "")
    local num, sufixo = s:match("([%d%.]+)([KMB]?)")
    if not num then return 0 end
    num = tonumber(num) or 0
    if sufixo == "K" then num = num * 1000
    elseif sufixo == "M" then num = num * 1000000
    elseif sufixo == "B" then num = num * 1000000000
    end
    return num
end

local function GetValorPS(brainrotObj)
    for _, child in pairs(brainrotObj:GetDescendants()) do
        if child:IsA("TextLabel") and child.Text:find("/s") then
            return ParseValorPS(child.Text)
        end
    end
    return 0
end

-- // FRAME PRINCIPAL
local Main = Instance.new("Frame", sg)
Main.Size = UDim2.fromOffset(400, 460)
Main.Position = UDim2.new(0.5, -200, 0.3, 0)
Main.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
Main.BorderSizePixel = 0
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 10)

local stroke = Instance.new("UIStroke", Main)
stroke.Color = Color3.fromRGB(0, 255, 127)
stroke.Thickness = 2

local TopBar = Instance.new("Frame", Main)
TopBar.Size = UDim2.new(1, 0, 0, 45)
TopBar.BackgroundTransparency = 1

local Title = Instance.new("TextLabel", TopBar)
Title.Size = UDim2.new(1, -50, 1, 0)
Title.Text = "AUTO FARM INFINITO"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18

local CloseBtn = Instance.new("TextButton", TopBar)
CloseBtn.Size = UDim2.fromOffset(30, 30)
CloseBtn.Position = UDim2.new(1, -35, 0, 7)
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Color3.new(1, 1, 1)
CloseBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 16
CloseBtn.BorderSizePixel = 0
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(0, 6)

CloseBtn.MouseEnter:Connect(function()
    CloseBtn.BackgroundColor3 = Color3.fromRGB(180, 30, 30)
end)
CloseBtn.MouseLeave:Connect(function()
    CloseBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
end)
CloseBtn.MouseButton1Click:Connect(function()
    FarmAtivo = false
    sg:Destroy()
end)

local Toggle = Instance.new("TextButton", Main)
Toggle.Size = UDim2.new(0.92, 0, 0, 42)
Toggle.Position = UDim2.new(0.04, 0, 0, 50)
Toggle.Text = "AUTO FARM: OFF"
Toggle.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Toggle.TextColor3 = Color3.new(1, 1, 1)
Toggle.Font = Enum.Font.GothamBold
Toggle.TextSize = 17
Toggle.BorderSizePixel = 0
Instance.new("UICorner", Toggle)

Toggle.MouseButton1Click:Connect(function()
    FarmAtivo = not FarmAtivo
    Toggle.Text = FarmAtivo and "FARMANDO... (ON)" or "AUTO FARM: OFF"
    Toggle.BackgroundColor3 = FarmAtivo and Color3.fromRGB(0, 100, 50) or Color3.fromRGB(35, 35, 35)
end)

local LabelValor = Instance.new("TextLabel", Main)
LabelValor.Size = UDim2.new(0.92, 0, 0, 20)
LabelValor.Position = UDim2.new(0.04, 0, 0, 100)
LabelValor.Text = "VALOR MÍNIMO /s (ex: 500K, 1M, 1B):"
LabelValor.TextColor3 = Color3.fromRGB(0, 255, 127)
LabelValor.BackgroundTransparency = 1
LabelValor.Font = Enum.Font.GothamBold
LabelValor.TextSize = 13
LabelValor.TextXAlignment = Enum.TextXAlignment.Left

local InputValor = Instance.new("TextBox", Main)
InputValor.Size = UDim2.new(0.92, 0, 0, 36)
InputValor.Position = UDim2.new(0.04, 0, 0, 122)
InputValor.PlaceholderText = "ex: 500K ou 1M ou 1B (vazio = qualquer valor)"
InputValor.Text = ""
InputValor.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
InputValor.TextColor3 = Color3.new(1, 1, 1)
InputValor.PlaceholderColor3 = Color3.fromRGB(100, 100, 100)
InputValor.Font = Enum.Font.Gotham
InputValor.TextSize = 13
InputValor.BorderSizePixel = 0
InputValor.ClearTextOnFocus = false
Instance.new("UICorner", InputValor)

InputValor.FocusLost:Connect(function()
    ValorMinimo = ParseInput(InputValor.Text)
    InputValor.TextColor3 = ValorMinimo > 0 and Color3.fromRGB(0, 255, 127) or Color3.new(1, 1, 1)
end)

local LabelBrainrot = Instance.new("TextLabel", Main)
LabelBrainrot.Size = UDim2.new(0.92, 0, 0, 20)
LabelBrainrot.Position = UDim2.new(0.04, 0, 0, 168)
LabelBrainrot.Text = "BRAINROTS (opcional, vazio = todos):"
LabelBrainrot.TextColor3 = Color3.fromRGB(0, 255, 127)
LabelBrainrot.BackgroundTransparency = 1
LabelBrainrot.Font = Enum.Font.GothamBold
LabelBrainrot.TextSize = 13
LabelBrainrot.TextXAlignment = Enum.TextXAlignment.Left

local ListBrainrot = Instance.new("ScrollingFrame", Main)
ListBrainrot.Size = UDim2.new(0.92, 0, 0, 220)
ListBrainrot.Position = UDim2.new(0.04, 0, 0, 190)
ListBrainrot.CanvasSize = UDim2.new(0, 0, 0, #Brainrots * 32)
ListBrainrot.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
ListBrainrot.ScrollBarThickness = 4
ListBrainrot.BorderSizePixel = 0
Instance.new("UICorner", ListBrainrot)
Instance.new("UIListLayout", ListBrainrot)

for _, name in ipairs(Brainrots) do
    local b = Instance.new("TextButton", ListBrainrot)
    b.Size = UDim2.new(1, 0, 0, 32)
    b.Text = name
    b.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    b.TextColor3 = Color3.new(0.7, 0.7, 0.7)
    b.Font = Enum.Font.Gotham
    b.TextSize = 14
    b.BorderSizePixel = 0

    b.MouseButton1Click:Connect(function()
        local lower = name:lower()
        if AlvosSelecionados[lower] then
            AlvosSelecionados[lower] = nil
            b.TextColor3 = Color3.new(0.7, 0.7, 0.7)
            b.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
        else
            AlvosSelecionados[lower] = true
            b.TextColor3 = Color3.fromRGB(0, 255, 127)
            b.BackgroundColor3 = Color3.fromRGB(20, 50, 35)
        end
        local count = 0
        for _ in pairs(AlvosSelecionados) do count = count + 1 end
        Title.Text = count == 0 and "AUTO FARM INFINITO" or count .. " SELECIONADO(S)"
    end)
end

-- // ARRASTAR
TopBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = Main.Position
    end
end)
TopBar.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)
UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

-- // BUSCA PROMPT
local function FindPrompt()
    local Brainrots_folder = workspace:FindFirstChild("Client") and
        workspace.Client:FindFirstChild("Path") and
        workspace.Client.Path:FindFirstChild("Brainrots")

    if not Brainrots_folder then return nil end

    local melhor = nil
    local melhorValor = -1

    for _, obj in pairs(Brainrots_folder:GetChildren()) do
        local prompt = obj:FindFirstChild("HumanoidRootPart") and
            obj.HumanoidRootPart:FindFirstChildOfClass("ProximityPrompt")
        if not prompt then continue end

        local texto = (prompt.ObjectText or ""):lower()
        if texto == "" then continue end

        -- checa brainrot selecionado
        local brainrotOk = next(AlvosSelecionados) == nil
        if not brainrotOk then
            for alvo in pairs(AlvosSelecionados) do
                if texto:find(alvo) then
                    brainrotOk = true
                    break
                end
            end
        end
        if not brainrotOk then continue end

        -- checa valor /s
        local valorPS = GetValorPS(obj)
        if valorPS < ValorMinimo then continue end

        if valorPS > melhorValor then
            melhorValor = valorPS
            melhor = prompt
        end
    end

    return melhor
end

-- // LOOP PRINCIPAL
task.spawn(function()
    while true do
        task.wait(0.1)
        if not FarmAtivo then continue end

        local prompt = FindPrompt()
        if not prompt then continue end

        local item = prompt.Parent
        local targetPart = item:IsA("BasePart") and item or item:FindFirstChildWhichIsA("BasePart")
        if not targetPart then continue end

        local char = LocalPlayer.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then continue end

        pcall(function()
            hrp.CFrame = targetPart.CFrame * CFrame.new(0, 2, 0)
            task.wait(0.05)
            prompt.HoldDuration = 0
            fireproximityprompt(prompt)
            task.wait(0.2)
        end)
    end
end)
