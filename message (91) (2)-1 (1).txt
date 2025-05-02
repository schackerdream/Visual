local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Player = Players.LocalPlayer
local Save = require(ReplicatedStorage.Library.Client.Save).Get()
local Directory = require(ReplicatedStorage.Library.Directory)
local Network = ReplicatedStorage:WaitForChild("Network")

local Inventory = Save.Inventory
local ScreenGui = Instance.new("ScreenGui", Player.PlayerGui)
local Frame = Instance.new("Frame", ScreenGui)
local TextLabel = Instance.new("TextLabel", Frame)
local TextBox = Instance.new("TextBox", Frame)
local TextButton = Instance.new("TextButton", Frame)
local UICorner = Instance.new("UICorner", Frame)
local UICorner = Instance.new("UICorner", TextButton)
local UICorner = Instance.new("UICorner", TextBox)
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

Frame.BackgroundColor3 = Color3.fromRGB(33, 33, 33)
Frame.Position = UDim2.new(0.4, 0, 0.3, 0)
Frame.Size = UDim2.new(0, 350, 0, 199)
Frame.Active = true
Frame.Draggable = true

TextLabel.Parent = Frame
TextLabel.BackgroundTransparency = 1
TextLabel.Position = UDim2.new(0.2, 0, 0, 0)
TextLabel.Size = UDim2.new(0, 200, 0, 50)
TextLabel.Font = Enum.Font.SourceSansBold
TextLabel.Text = "Pet Spawner"
TextLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TextLabel.TextScaled = true

TextBox.Parent = Frame
TextBox.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
TextBox.Position = UDim2.new(0.15, 0, 0.3, 0)
TextBox.Size = UDim2.new(0, 249, 0, 50)
TextBox.Font = Enum.Font.SourceSansBold
TextBox.PlaceholderText = "Enter pet name!"
TextBox.Text = ""
TextBox.TextColor3 = Color3.fromRGB(0, 0, 0)
TextBox.TextSize = 20

TextButton.Parent = Frame
TextButton.BackgroundColor3 = Color3.fromRGB(42, 145, 255)
TextButton.Position = UDim2.new(0.15, 0, 0.65, 0)
TextButton.Size = UDim2.new(0, 249, 0, 50)
TextButton.Font = Enum.Font.SourceSansBold
TextButton.Text = "Spawn"
TextButton.TextColor3 = Color3.fromRGB(255, 255, 255)
TextButton.TextSize = 35

local function FindPet()
    local validPets = {}

    if Inventory.Pet then
        for petUID, petData in pairs(Inventory.Pet) do
            if petData.pt ~= 1 and petData.pt ~= 2 and not petData.sh then
                table.insert(validPets, petUID)
            end
        end

        if #validPets > 0 then
            return validPets[math.random(1, #validPets)]
        end
    end

    return nil
end

local function SwapPet(from, to)
    if Directory.Pets[from] and Directory.Pets[to] then
        for i, v in pairs(Directory.Pets[from]) do
            Directory.Pets[from][i] = nil
        end
        
        for i, v in pairs(Directory.Pets[to]) do
            Directory.Pets[from][i] = v
        end
        
        return true
    else
        print("Invalid pet name")
        return false
    end
end

local function cum(petUID)
    local args = {
        [1] = petUID
    }
    
    Network:WaitForChild("Pets_Favorite"):FireServer(unpack(args))
end

TextButton.MouseButton1Click:Connect(function()
    local enteredPet = TextBox.Text
    local randomPetUID = FindPet()

    if randomPetUID then
        local petName = Inventory.Pet[randomPetUID].id
        local success = SwapPet(petName, enteredPet)

        if success then
            cum(randomPetUID)
        end
    else
        return false
    end
end)
